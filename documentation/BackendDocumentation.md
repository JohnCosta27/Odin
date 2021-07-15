# Odin - Backend documentation

## Technology used

For the backend I am using Node.js and Express.js to create a RESTful API which my front-end application interacts with. 
For authentication I am using JSON Web Tokens (JWT), with Auth0 as the authentication and accounts provider. This means I do not need to use sessions and store them in a database.

I also use a Storage bucket is Supabase, to upload revision content for the website.

A relational diagram is in this folder.

## V1 - First version

At this stage I have working prototype of Odin, it has decent functionality but it still has a lot of tweaking needed, mostly in the front-end so I won't go into much detail.

### Structure

index.js file contains the starting point of the application. It starts the Express.js app and imports the routers from the .routers.js files, which live in routers/.

There also a few helper files which help keep things seperate.
* ./auth/supabase.js - There so I can call the supabase-js library without having to write down the initialisation every time, it exports the const supabase
* ./auth/check-jwt.js - Returns the checkJwt function which checks if a certain jwt is valid, to be used as a middleware in express methods. It uses 'express-jet' and 'jwks-rsa'
* ./config/env.dev.js - Helper file which returns the .env variables, as well as throw an error if they do no exist.
* ./routers/messages.service.js - A file to keep the various messages that I send out as responses consitent and in one place.
* ./routers/''.routers.js - An express router, which is exported and used in the index.js file. Routers can also have another middleware which checks the permissions of a certain JWT. More details below.

### Express routers and methods
* /api - Handles very basic calls, only router declared and used inside index.js.
	* /sync - A way to keep the Auth0 users and supabase users synchronised. It tries to write the jwt.sub to the database as a user. If an error is returned then the user already exists. No error handling is done as it isn't necessary.
* subjects - Handles most things to do with subjects and subject content from the database
	* /create - Inserts a new subject into the subjects table in the Postgres database.
		* Permission: 'create:subjects'
	* /createpoints - Inserts various points into the database.
		* Permission: 'create:subjectpoint'
	* /getall - Get all the subjects.
	* /getnew - Weird name, get the subjects that the user that made the request is not taking
	* /get?subjectid - Gets the subject, all the topics in the subject and all the points in those topics.
	* /getusersubjects - Gets the subjects that the user is taking.
	* /getalltopics?subjectid - Gets all the topics from a certain subject
	* /getpoints?topicid - Gets all the points from a topic
	* /getpoint?pointid - Get a point
	* /getpointrevision?pointid - Gets the points with topic information that the user has revised.
* files - Manages everything to do with the storage bucket in supabase. It uses 'multer' which allows express to take files as middleware into its requests.
	* /upload - Takes a file (usually an image) and uploads it to the storage bucket using HTTP requests as supabase does not support server-side uploading to storage buckets.
	* /getnotes?pointid - Gets all revision notes for a point.
* users - Manages information about users.
	* /addsubject - Inserts a new subject into the student_subjects table.
* progress - Handles user progress through subjects
	* /get - Gets all the points with topic and subject information joined that the user has revised.
	* /getprogressinsubject - Gets the points that the user has revised with other information, and formats it.
	* /add - Inserts various revision points for the user.
	* /addsingular - Only adds a singular revision point.
