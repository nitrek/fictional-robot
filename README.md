
# fictional-robot

## Problem Statement:

You need to build a Rest API where an  `instructor`  and  `student`  manage their  `Webinars`  &  `Courses`. A Creator can create  `course`,  `subjects`,  `tags`  and they can upload  `videos`  and  `webinars`  to the system. Lessons & webinars can be present in multiple  `courses`  and  `subjects`. A Student can then search for webinars and/or videos using  `webinar title`,  `video title`,  `course name`  and  `subject name`, and can filter using  `course`,  `subjects`  and  `tags`. Build the application keeping in mind that data duplicacy and time complexity should be minimized.
## User Stories 

 *  As an instructor, I can upload a webinar.
	 * using `/webinar` api
 *   As an instructor, I can create, edit, delete course.
	 * using `course microservice` 
 *  As an instructor, I can create, edit, delete subjects.
	 * using `subject microservice` 
 *   As an instructor, I can create, edit, delete tags.
	 * using `tags microservice` 
 *  As an instructor, I can upload a video.
	 * using `upload video api of video  microservice`
 *  As an instructor, I can add new tag while uploading video or webinar.
 	 * using `Create Tag API of tags microservice` 
 *   As an instructor, I can see the most viewed videos, courses and webinars.
	 * using `list api of course/subject/video/webinar api and subblying the orderby param as views`
 *  As a student, I can see list of webinars & videos.
	 *  using `list api of video/webinar`
 *   As a student, I can search webinars & videos by title.
	 * using ` seach api `
 *  As a student, I can filter webinars & videos by course, subjects, tags.
	 *  using `search api`
 *  As a student, when I am playing a video or a webinar, I can get personalized suggestions of courses/webinars.
	 * using `recommendation api`

## Technology and Infrastructure 
* Micro Services to be coded in different languages i would code them in kotlin or python
	* Google Cloud is my Prefered hosting platform.
	* App Engine / Compute Engine or Managed Instance Groups  could be used for hosting the apis
	* GKE ( Kubernates Engine  can be considered based on the scale, only prefered if it's a large scale application )
* Postgres Database can be used to stores all the structured data of the apis
	* GCP Cloud SQL Postgres is good option as it's managed PostgreSQL Service With High Availability.
* Google Storage Bucket can be used for storing blob data like the raw video files
	* It's Provides High Availability and Cross Regional Replicas for low latency 
	* Also has very Low Archival options for move old videos to save cost.
* 
## List of Micro-Services 
* Authentication & Authorisation Service 
* Tags Service
* Video Service 
* Webinar Service
* Coursers Service
* Subject Service
* Discovery(Search) Service



## Authentication & Authorisation Service APIs and Tables

 Tables:
```
1 > Users : id | username | roleid | password | lastsuccesslogin | accountactive | lastloginfrom 
2 > Roles  : id | rolename | approvedapilist
eg roles Teacher , student ,admin etc.
```
 * Login API
	* Name : `/login`
	* Table used : users and roles 
	* Method : `POST`
	* Input 
		 `{"username":"nitrek","password":"userpass"}`
	* Output 
		* On Success : `jwt token`
		* On Failure : `403`

## Tags Microservice 
 Tables:
```
1 > Tags : id | contentType(webinar/courses/Subject/video) | content-id| tag ( many to many mapping)
2>  Related Tags : id | tag1id | tag2id
```
 * Create Tag API
	* Name : `/tag`
	* Table used : tag
	* HTTPMethod : `POST`
	* Input 
		 `{"content-id","tag list"}`
	* Output 
		* On Success : `200`
		* On Failure : `500`
	* Remarks : Api fetched Content type from content id.
* Delete Tag API
	* Name : `/tag`
	* Table used : tag
	* HTTPMethod : `DELETE`
	* Input 
		 `{"tag-id"}`
	* Output 
		* On Success : `200`
		* On Failure : `403,500`
##  Video Microservice 
 Tables:
```
1 > Video : id | name | title | desiription | video type(webinar/course video etc) | views
```
Other Microservices this uses
```
Tag microservice 

```
 * Upload Video API : Upload Video to GCP Storage.
	* Name : `/video`
	* Table used : video
	* HTTPMethod : `POST`
	* Input 
		 `{"name ","title ","desiription","video type",Video File,tags list}`
	* Output 
		* On Success : `200`
		* On Failure : `403,500`
	* Remarks : Using tags api to save tags using  `GET /tag` api passing the video id content id

 * Stream  Video API : Stream vidoe file via api.
	* Name : `/video`
	* Table used : `video`
	* HTTPMethod : `GET`
	* Input 
		 `videoid`
	* Output 
		* On Success : `200`
		* On Failure : `403,500`
	* Remarks : Using tags api to save tags using  `GET /tag` api passing the video id content id

 * List Videos.
	* Name : `/videos`
	* Table used : Webinar 
	* HTTPMethod : `GET`
	* Input : `<optional> search keyword or tag(s),<optional>orderby(views)`
	* Output 
		* On Success : `List of all videos based on input and role`
		* On Fail : `200`
## Webinar Service API and Tables 
 Tables:
```
1 > Webinar : id | name | title | description | video-id | views
```
Other Microservices this uses
```
Tag & Video microservice 

```
 * List Webinar.
	* Name : `/webinar`
	* Table used : Webinar 
	* HTTPMethod : `GET`
	* Input : `<optional> search keyword or tag(s),<optional>orderby(views)`
	* Output 
		* On Success : `List of all webinars based on input and role`
		* On Failure : `403,500`

* Create Webinar 
	* Name : `/webinar`
	* Table used : `webinar` 
    * Other Api used : ` post /video and post /tags`
	* HTTPMethod : `POST`
	* Input : `name,title,description,videofile,<optional>tag(s)`
	* Output 
		* On Success : `200`
		* On Failure : `403,500`
	* Remarks : Using video api to upload video , tag api  to create tag.


## Courses APIS and Tables
```
1 > Courses : id | name | title | description | video-id(s) | webinar-id(s) | views
2 > CourseTeacher : id | course-id | teacher-id(userid) ( many to many mapping )
3 > CourseStudents : id | course-id | Student-id(userid) ( many to many mapping)
```
Other Microservices this uses
```
Tag & Video microservice 

```
 * List courses .
	* Name : `/courses `
	* Table used : `Courses` 
	* HTTPMethod : `GET`
	* Input : `<optional> search keyword or tag(s),<optional>orderby(views)`
	* Output 
		* On Success : `List of all courses based on search or tag if provided`
		* On Failure : `403,500`

 * List course Webinars/Videos.
	* Name : `/coursesWebinarsVideos`
	* Table used : `Courses` 
	* HTTPMethod : `GET`
	* Input : `courseid,type(webinar/videos/both)`
	* Output 
		* On Success : `List of all webinars/video(based on type in input) based on courseid`
		* On Failure : `403,500`

 * My courses .
	* Name : `/mycourses `
	* Table used : `Courses ,CourseTeacher  ,CourseStudents ` 
	* HTTPMethod : `GET`
	* Input : `<optional> search keyword or tag(s)`
	* Output 
		* On Success : `List of all  my course based on input and role`
		* On Failure : `403,500`

 * Enroll in course .
	* Name : `/enrollcourse `
	* Table used : `Courses,CourseStudents ` 
	* HTTPMethod : `GET`
	* Input : `courseid`
	* Output 
		* On Success : `200`
		* On Failure : `403,500`

* Create courses 
	* Name : `/course`
	* Table used : `Courses,CourseTeacher` 
    * Other Api used : ` post /video and post /tags`
	* HTTPMethod : `POST`
	* Input : `name,title,description,<optional>videofile(s),<optional>videoid(s),<optional>webinarid(s),<optional>tag(s)`
	* Output 
		* On Success : `200`
		* On Failure : `403,500`
	* Remarks : Using video api to upload video , tag api  to create tag.

 * Update courses 
	* Name : `/course`
	* Table used : `Courses,CourseTeacher` 
    * Other Api used : ` post /video and post /tags`
	* HTTPMethod : `PUT`
	* Input : `name,title,description,<optional>videofile(s),<optional>videoid(s),<optional>webinarid(s),<optional>tag(s)`
	* Output 
		* On Success : `200`
		* On Failure : `403,500`
	* Remarks : Using video api to upload video , tag api  to create tag.
 
 * Delete course.
	* Name : `/course`
	* Table used : `Courses,CourseTeacher,CourseStudents ` 
	* HTTPMethod : `DELETE`
	* Input : `courseid`
	* Output 
		* On Success : `200`
		* On Failure : `403,500`
	
## Subjects APIs and Tables
```
1 > Subjects: id | name | title | description | video-id(s) | webinar-id(s) | views
2 > SubjectTeacher : id | subject-id | teacher-id(userid) ( many to many mapping )
3 > SubjectStudents : id | subject-id | student-id(userid) ( many to many mapping)
```
Other Microservices this uses
```
Tag & Video microservice 

```
 * List subjects.
	* Name : `/subject`
	* Table used : `subject` 
	* HTTPMethod : `GET`
	* Input : `<optional> search keyword or tag(s),<optional>orderby(views)`
	* Output 
		* On Success : `List of all subject based on search or tag if provided`
		* On Failure : `403,500`

 * List subject  Webinars/Videos.
	* Name : `/subject  WebinarsVideos`
	* Table used : `subject`
	* HTTPMethod : `GET`
	* Input : `courseid,type(webinar/videos/both)` `
	* Output 
		* On Success : `List of all webinars/video(based on type in input) based on courseid`
		* On Failure : `403,500`

 * My subjects.
	* Name : `/mysubject `
	* Table used : `subject,SubjectTeacher ,SubjectStudents ` 
	* HTTPMethod : `GET`
	* Input : `<optional> search keyword or tag(s)`
	* Output 
		* On Success : `List of all  my subject based on input and role`
		* On Failure : `403,500`

 * Enroll in subject.
	* Name : `/enrollsubjects `
	* Table used : `subject,SubjectStudents ` 
	* HTTPMethod : `GET`
	* Input : `subjectid`
	* Output 
		* On Success : `200`
		* On Failure : `403,500`

* Create subject
	* Name : `/subject`
	* Table used : `subject,subjectTeacher`
    * Other Api used : ` post /video and post /tags`
	* HTTPMethod : `POST`
	* Input : `name,title,description,<optional>videofile(s),<optional>videoid(s),<optional>webinarid(s),<optional>tag(s)`
	* Output 
		* On Success : `200`
		* On Failure : `403`
	* Remarks : Using video api to upload video , tag api  to create tag.

* Update subject
	* Name : `/subject`
	* Table used : `subject,subjectTeacher`
    * Other Api used : ` post /video and post /tags`
	* HTTPMethod : `PUT`
	* Input : `name,title,description,<optional>videofile(s),<optional>videoid(s),<optional>webinarid(s),<optional>tag(s)`
	* Output 
		* On Success : `200`
		* On Failure : `403`
	* Remarks : Using video api to upload video , tag api  to create tag.

 * Delete subject.
	* Name : `/subject`
	* Table used : `subject,SubjectStudents,SubjectTeacher  ` 
	* HTTPMethod : `DELETE`
	* Input : `subjectid`
	* Output 
		* On Success : `200`
		* On Failure : `403,500`

# Search & Recommendation Service

Other Microservices this uses
```
all above microservice 

```
* Search 
	* name : `/search`
	*  HTTPMethod : GET
	* Input : `search tearm,<optional>findid(title/tag/description)`
	* Output : `list of courses,subjects,videos and webinars`
	* Remarks : Simple search that tokenises the search term and finds it in tags , titles 

* recommended Course 
	*  name : `/recommendation`
	*  HTTPMethod : GET
	* Input : `video-id`
	* Output : `list of courses,subjects,videos and webinars related to video`
	* Remarks : uses video id to get all related tags of the video and corresponding course and return related course,subject etc sorting by popularity 

