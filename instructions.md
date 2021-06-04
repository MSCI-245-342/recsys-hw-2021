# Recsys - Recommender Systems

This programming assignment builds off of the corresponding lab, and you should have read the lab's instructions before proceeding: https://github.com/MSCI-245-342/recsys-lab/blob/main/instructions.md  

## To Get Started

`cd` into the git repo directory you have created by cloning the lab's repo.

Run `bundle install`

## More about using Sequel

The lab talks about providing arguments to queries via the use of question marks.  

If you have integer variable, to provide this value to a query, you merely provide the variable as an argument.  For example:

```ruby
# Here is an example of passing an integer argument. 
id = 1
user_with_id_1 = DB.fetch( "select * from users where id = ?", id )
```
I encourage you to examine the queries that are formed:
```ruby
puts "Query is user_with_id_1.sql = \"#{user_with_id_1.sql}\""
```
To make sure the query as you expect it.  Also, I encourage you to work hand in hand with psql and take a query and run it in psql to make sure it works as you expect it to.  Many times, it is best to work on your query in psql, and then try to figure out how to put it in your program.

When you run a SQL select query, you will always get back a new relation.  Using Sequel, this means that it will always return rows for you to process.  Sometimes you might get zero rows, one row, or multiple rows.

You can check the number of rows returned via `.count`, for example:
```ruby
puts "Number of rows = #{user_with_id_1.count}"
```
For the above query, there will only be at most one user with a given id, for id is a primary key of the users table.

When you have a single row in the result, you can access it easily via `.first`, for example:
```ruby
# we can get the first row (which is the only row for this query)
print "Result is a single row, which is a hash: "
p user_with_id_1.first
puts "The user's name is: #{user_with_id_1.first[:name]}"
```
Here is another example of a query that returns a single **row**:
```ruby
# we can use AS to rename the result columns.
result = DB.fetch( "select avg(rating) as avg_rating from ratings" ) 
puts
puts "The average book ratings is %.1f" % [ result.first[:avg_rating] ]
```

When you provide arguments to Sequel, it pays attention to the type of the data in the argument.  For example, an integer is passed directly and shows up in the query as you expect it to.  When the argument is a string, it will be quoted with single quotes.  This is why in the lab, when we wanted to search for a book title, we did this:
```ruby
title_search = DB.fetch( "select * from books where lower(title) like ? order by title", "%#{title}%" ) 
```
where `title` is a variable holding the input from a user.  This produces the query as we expect it to (let title = 'hitch'):
```sql
select * from books where lower(title) like '%hitch%' order by title
```
but, if on the other hand we tried this:
```ruby
title_search = DB.fetch( "select * from books where lower(title) like '%?%' order by title", title ) 
```
we would get this broken SQL:
```sql
select * from books where lower(title) like '%'hitch'%' order by title
```
Most of the time, you will not have this think about this, and things will work as you want, but other times, you need to be a bit more clever such as the title search above.

### What NOT to do

Do not use string interpolation to inject values into your queries:
```ruby
title_search = DB.fetch( "select * from books where lower(title) like '%#{title}%' order by title" ) 
```
This produces a valid query, but it opens you up to a SQL injection attack by your user.  In MSCI 245, you'll lose points for doing this, in the real world, you are likely to lose your job or worse when the hackers destroy data, release data, etc.

## Tasks

These are the tasks from the lab, plus some additional tasks.  If you completed the lab, that is great.  You are welcome to copy the code and use it here in the homework.  Please be sure to acknowledge the help of your teammates as appropriate.

In the file `bookinfo.rb`, we have some code that will allow a user to search for and select a book based on its title.  Go ahead and run it: `ruby bookinfo.rb`

You are to finish this program by printing out a "page" about the book with the following features in this order:

### Feature: Title and Author
On a book info page, we should see the book's title and author, clearly identified as such.

### Feature: Number of Ratings
We should see a report of the number of ratings the book has received.

### Feature: Average Rating
We should see a report of the book's average rating.  If a book has no ratings, the average should be reported as "N/A".

### Feature: Fans
We should see a list of fans of the book.  This list should be at most 5 users long.  To be a fan, a user has to rate the book a 5.  To limit the number of rows, you use SQL [LIMIT](https://www.postgresql.org/docs/current/queries-limit.html).  Clearly identify your list as "Fans of this book include:" and print the users' names, but not their ids.

### Feature: Fans also like

We should see a list of books under the heading of "Fans of this book, also like these books:".  

For the fans of the book, get all of their bookratings, get the average rating for each book, and for books with an average fan rating of > 4.0, and for books that have at least 2 fans liking them, list them as "Fans of this book, also like these books:". 

Be sure to exclude "this book" from the list.  For example, fans of Inkheart are not "also fans" of Inkheart. 

Note: you are only using the ratings of these fans and not all ratings.  If there are not any books meeting these conditions, you should not output the "Fans of this book" feature.

### Feature: Other books by this author

We should see a list of the other books written by the author of this book.  If there are no other books written by this author, this feature should not be output.

## Other Notes

Your bookinfo "page" output should be nicely formatted and easy to read.  

For these tasks,  it is up to you how much you want to do with SQL, and how much you want to do in Ruby.  For example, you don't have to use SQL to compute the averages, but maybe that is easier than computing the averages in Ruby.

In many cases, a few more minutes with SQL can save you lots of coding.  Try to get the database to do most of the work for you.

# Turning in your work

Edit the README.md file to include your name and acknowledge assistance given and received.  

You **must** sign the acknowledgments in the README.md file or you will be severely penalized with a loss of points.

Commit and push your code to GitHub.  Your submission time for this work will be the time of your last commit.




















