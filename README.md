# SQL CRUD
## Restaurant Finder

### Creating the Restaurant Table
'''
CREATE TABLE restaurants (
    id INT PRIMARY KEY,
    name TEXT NOT NULL,
    category TEXT NOT NULL,
    price_tier TEXT CHECK(price_tier IN ('cheap', 'medium', 'expensive')),
    neighborhood TEXT NOT NULL,
    opening_hours TEXT NOT NULL, -- Stored as 'HH:MM-HH:MM'
    average_rating REAL CHECK(average_rating >= 0 AND average_rating <= 5),
    good_for_kids BOOLEAN NOT NULL
);
'''
### Creating the Reviews Table
'''
CREATE TABLE reviews (
    id INTEGER PRIMARY KEY,
    restaurant_id INTEGER NOT NULL,
    user_name TEXT NOT NULL,
    comment TEXT,
    created_at DATE DEFAULT CURRENT_DATE,
    FOREIGN KEY (restaurant_id) REFERENCES restaurants(id)
);
'''
### Import Practice CSV
'''
.mode csv
.import data/restaurants.csv restaurant
'''
### 1. Find all cheap restaurants in a particular neighborhood (pick any neighborhood as an example).
'''
SELECT * FROM restaurants
WHERE price_tier = 'cheap' AND neighborhood = 'Harlem';
'''
### 2. Find all restaurants in a particular genre (pick any genre as an example) with 3 stars or more, ordered by the number of stars in descending order.
'''
SELECT * FROM restaurants
WHERE category = 'Italian' AND average_rating >= 3
ORDER BY average_rating DESC;
'''
### 3. Find all restaurants that are open now 
'''
SELECT * FROM restaurants
WHERE substr(opening_hours, 1, 5) <= '15:00'
AND substr(opening_hours, 7, 5) >= '15:00';
'''
### 4. Leave a review for a restaurant 
'''
INSERT INTO reviews (restaurant_id, user_name, rating, comment)
VALUES (1, 'John Doe', 5, 'Excellent service and food!');
'''
### 5. Delete all restaurants that are not good for kids.
'''
DELETE FROM restaurants
WHERE good_for_kids = FALSE;
'''
### 6. Find the number of restaurants in each NYC neighborhood.
'''
SELECT neighborhood, COUNT(*) AS num_restaurants
FROM restaurants
GROUP BY neighborhood;
'''

## Social Media App

### Creating Table for Users
'''
CREATE TABLE users (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    email TEXT NOT NULL UNIQUE,
    password TEXT NOT NULL,
    handle TEXT NOT NULL UNIQUE
);
'''
### Creating Tables for posts
'''
CREATE TABLE posts (
    post_id INTEGER PRIMARY KEY AUTOINCREMENT,
    user_id INTEGER,
    recipient_id INTEGER,
    content TEXT NOT NULL,
    post_type TEXT NOT NULL CHECK(post_type IN ('message', 'story')),
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    visible INTEGER DEFAULT 1, -- 1 for visible, 0 for invisible
    FOREIGN KEY(user_id) REFERENCES users(user_id),
    FOREIGN KEY(recipient_id) REFERENCES users(user_id)
);
'''
### Import data
'''
.mode csv
.import data/users.csv
.import data/posts.csv
'''
### Register a new user
'''
INSERT INTO users (email, password, handle) VALUES ('newuser@example.com', 'securepassword', 'newuserhandle');
'''
### Create a new message
'''
INSERT INTO posts (user_id, recipient_id, content, post_type, visible) VALUES (1, 2, 'Hello, this is a private message!', 'message', 1);
'''
### Create new story
'''
INSERT INTO posts (user_id, content, post_type, visible) VALUES (1, 'This is a story.', 'story', 1);
'''
### 10 most recent visible Messages and Stories, in order of recency.
'''
SELECT * FROM posts WHERE visible = 1 ORDER BY created_at DESC LIMIT 10;
'''
### 10 most recent visible Messages sent by a particular User to a particular User (pick any two Users for example), in order of recency.

'''
SELECT * FROM posts WHERE user_id = 1 AND recipient_id = 2 AND post_type = 'message' AND visible = 1 ORDER BY created_at DESC LIMIT 10;
'''

### Make all Stories that are more than 24 hours old invisible.
'''
UPDATE posts SET visible = 0 WHERE post_type = 'story' AND ROUND((JULIANDAY('now') - JULIANDAY(created_at)) * 24) > 24;
'''
### Show all invisible Messages and Stories, in order of recency.
'''
SELECT * FROM posts WHERE visible = 0 ORDER BY created_at DESC;
'''
### Show the number of posts by each User.
'''
SELECT user_id, COUNT(*) AS post_count FROM posts GROUP BY user_id;
'''
### Show the post text and email address of all posts and the User who made them within the last 24 hours.
'''
SELECT p.content, u.email FROM posts p JOIN users u ON p.user_id = u.user_id WHERE ROUND((JULIANDAY('now') - JULIANDAY(p.created_at)) * 24) <= 24;
'''
### Show the email addresses of all Users who have not posted anything yet.
'''
SELECT email FROM users WHERE user_id NOT IN (SELECT DISTINCT user_id FROM posts);
'''



