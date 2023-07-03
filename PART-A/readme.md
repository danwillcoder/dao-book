# Dao Book
## Clinic Management Software

Dao Book is a clinic management software made specifically for Chinese medicine practitioners. In its earliest version, it will provide support for herbalists, and in later versions it may integrate functionality for acupuncture and other branches of Chinese medicine. 

<br>

-------------------
### **Purpose**
-------------------

Dao Book exists to: 

- streamline the consultation process for practitioners
- provide a long-term and affordable solution to storing patient notes and prescription histories
- provide the ability for patients to access their own prescription history 

<br>

-------------------
### **Functionality & Features**
-------------------
***MVP***

The foundation application will allow the practitioner to:

- Create new patient profiles
- Create individual session notes
- Record prescriptions
- View all patients relevant to the practitioner
- View all session notes and prescriptions for all patients relevant to the practitioner


#### ***Further features*** 



To further fulfil the above mentioned purposes, the application will ideally also include the following features:

- Within each session record, the practitioner can select to have an automated email sent to the patient that will include: the presciption, dosage and administration, other lifestyle recommendations. 
- Patients will be able to access their own prescription history through logging into the platform.

<br>

------------------- 
### **Target Audience**
-------------------

Chinese medicine practitioners who want a streamlined and simplified patient record software that is specified to their industry, who in addition value their patients being able to access their own prescription data. 

<br>

-------------------
### **Tech Stack**
-------------------

- MongoDB
- Express.js
- React.js (Vite)
- Node.js
- Express Router
- TailwindCSS
- Json Web Tokens
- Nodemailer
<br>

<br><br><br>

-------------------
## Data Flow Diagrams
-------------------

#### REGISTER

1. The client submits a POST request containing the new practitioner's email and password. This POST request comes from an HTML form.
2. The request content is validated, ensuring all required fields are populated and of the correct datatype. In this case, the email and password must be a string, in the form of an email, and the password must pass minimal security requirements. If this is not the case, an error is thrown to Step 3. Otherwise, proceed to Step 4.
3. The error is returned by the server containing information about the incorrectly supplied request.
4. The server upserts the user into the database, which ensures we only require one trip to the database.
5. The database receives an email and password for a given user. If the user does not exist, it will be created. If the user exists already, nothing happens. If the DB encounters an error, it's thrown to step 6. On a successful insert, proceed to Step 7.
6. The error is returned by the server. This error remains vague as to not divulge DB information.
7. A JWT is issued and returned to the client.
8. The JWT complies to the OAuth spec, containing an access_token & token_type in JSON format.
9. The response from server contains either an Error object or a valid JWT. The client then redirects the successfully logged-in user.

#### LOGIN

1. The client submits a POST request containing the practitioner's email and password. This POST request comes from an HTML form.
2. The request content is validated, ensuring all required fields are populated and of the correct datatype. In this case, the email and password must be a string, in the form of an email, and the password must pass minimal security requirements. If this is not the case, an error is thrown to Step 3. Otherwise, proceed to Step 4.
3. The error is returned by the server containing information about the incorrectly supplied request.
4. The database is queried for the user. If the user does not exist, an error is thrown to Step 6. If the user's password hash does not match the password hash in the database, an error is thrown to Step 6. Otherwise, proceed to step 7.
5. We retrieve the entire User document from the database before validating the password.
6. The error returned by the server does not contain information about whether the account exists or not, as this may be used maliciously for enumeration. 
7. A JWT is issued and returned to the client.
8. The JWT complies to the OAuth spec, containing an access_token & token_type in JSON format.
9. The response from server contains either an Error object or a valid JWT. The client then redirects the successfully logged-in user.

#### REGISTER (Patient)

1. The client submits a POST request containing the new patient's email and password. This POST request comes from an HTML form.
2. The request content is validated, ensuring all required fields are populated and of the correct datatype. In this case, the email must be a string, in the form of an email. The request must contain a valid JWT, as only practitioners are able to register patients. If this is not the case, an error is thrown to Step 3. Otherwise, proceed to Step 4.
3. The error is returned by the server containing information about the incorrectly supplied request.
4. The server upserts the patient user into the database, which ensures we only require one trip to the database.
5. The database receives an email and password for a given user. If the user does not exist, it will be created. If the user exists already, nothing happens. If the DB encounters an error, it's thrown to step 6. On a successful insert, proceed to Step 7.
6. The error is returned by the server. This error remains vague as to not divulge DB information.
7. A confirmation message is generated by the server.
8. This confirmation message contains a HTTP code 201 and a confirmation message in JSON format.
9. The response from server contains either an Error object or a confirmation message.

#### LOGIN (Patient)

1. The client submits a POST request containing the patient's email. This POST request comes from an HTML form.
2. The request content is validated, ensuring all required fields are populated and of the correct datatype. In this case, the email must be a string, in the form of an email.  If this is not the case, an error is thrown to Step 3. Otherwise, proceed to Step 4.
3. The error is returned by the server containing information about the incorrectly supplied request.
4. The email is used to make a database call to determine if the patient user exists. If the user does not exist, an error is thrown to Step 6. Otherwise, proceed to Step 7.
5. The patient document is retrieved. 
6. The error is returned by the server. This error remains vague as to not divulge DB information.
7. A magic link is created and sent via email to the user's email address. This email contains a link with a query parameter of token and a value of a JWT. A confirmation is sent to the client. 
8. This confirmation message contains a HTTP code 200 and a confirmation message in JSON format.
9. The user opens their email and clicks the link. This triggers a GET request to the server.
10. The JWT in the token field is validated. If successful, a confirmation message is sent with a status code of 200. If it fails, an error is sent.
11. If the client receives a successful JWT, the user is redirected to the application. Otherwise, an error is displayed.

#### GET PATIENT LIST

1. The client submits a GET request. This GET request comes with a JWT token in the Header.
2. The JWT is validated. If it fails validation, an error is thrown to Step 3. Otherwise, proceed to Step 4.
3. The error is returned by the server containing information about the incorrectly supplied request with a status code of 403 Unauthorized.
4. Using the practitionerId inside the JWT, we make a DB request for all the patients associated with the ID. If unsuccessful, an error is thrown to Step 5. Otherwise, proceed to Step 6.
5. The error is returned by the server. This error remains vague as to not divulge DB information.
6. The practitionerId is used to get all patient Documents from the server. 
7. The list of patients is sent in a JSON format to the client.
8. The response from the server contains either an Error object or the patient list.

#### UPDATE PATIENT INFO

1. The client submits a PUT request. This PUT request contains a JWT in the Header, and JSON as the body containing the patientId, and the new patient information.
2. The request is validated. In this case, the patientId must be a string. The fields must be strings. If it fails validation, an error is thrown to Step 3. Otherwise, proceed to Step 4.
3. The error is returned by the server containing information about the incorrectly supplied request with a status code of 403 Unauthorized.
4. The JWT is validated. If it fails validation, an error is thrown to Step 5. Otherwise, proceed to Step 6.
5. The error is returned by the server containing information about the incorrectly supplied request with a status code of 403 Unauthorized.
6. The patientId is used to send an update to the database. If unsuccessful, an error is thrown to Step 8. Otherwise, proceed to step 9.
7. The patientId updates the Document in the database. The update returns the Document.
8. The error is returned by the server. This error remains vague as to not divulge DB information.
9. The updated patient info is sent in a JSON format to the client.
10. The response from the server contains either an Error object or the new patient Document.

#### CREATE CONSULTATION

1. The client submits a POST request containing the new consultation information. This POST request comes from an HTML form.
2. The request content is validated, ensuring all required fields are populated and of the correct datatype. In this case, the consultation fields must be strings, or a date. If this is not the case, an error is thrown to Step 3. Otherwise, proceed to Step 4.
3. The error is returned by the server containing information about the incorrectly supplied request.
4. The JWT is validated. If it fails validation, an error is thrown to Step 5. Otherwise, proceed to Step 6.
5. The error is returned by the server containing information about the incorrectly supplied request with a status code of 403 Unauthorized.
6. The patientId is confirmed to exist. If the patient does not exist, we throw an Error to Step 8. Otherwise, proceed to Step 9.
7. The patientId is sent to the database.
8. The error is returned by the server. This error remains vague as to not divulge DB information.
9. A new consultation is created on the server and populated with the fields from the request. This document is sent to the database. If an error is encountered, it is sent instead of the new consultation.
10. The consultation is a new Document.
11. The response from server contains either an Error object or the new Consultation with a status code of 201.

#### GET CONSULTATIONS

1. The client submits a POST request. This POST request contains a JWT in the Header and JSON as the body containing the patientId.
2. The request is validated. In this case, the patientId must be a string. If it fails validation, an error is thrown to Step 3. Otherwise, proceed to Step 4.
3. The error is returned by the server containing information about the incorrectly supplied request with a status code of 403 Unauthorized.
4. The JWT is validated. If it fails validation, an error is thrown to Step 5. Otherwise, proceed to Step 6.
5. The error is returned by the server containing information about the incorrectly supplied request with a status code of 403 Unauthorized.
6. All consultations related to that patient are requested from the database. If the patient does not exist, no consultations are returned, but the request is still considered valid. If another error is encountered, it is thrown to Step 8. Otherwise, proceed to Step 9.
7. The patientId is sent to the database.
8. The error is returned by the server. This error remains vague as to not divulge DB information.
9. All consultation Documents related to the patientId are sent to the client.
10. The response from server contains either an Error object or the consultation list with a status code of 200.

#### UPDATE CONSULTATION

1. The client submits a PUT request. This PUT request contains a JWT in the Header, and JSON as the body containing the consultationId and the new consultation info.
2. The request is validated. In this case, the consultationId must be a string. The fields must be strings. If it fails validation, an error is thrown to Step 3. Otherwise, proceed to Step 4.
3. The error is returned by the server containing information about the incorrectly supplied request with a status code of 400 Bad Request.
4. The JWT is validated. If it fails validation, an error is thrown to Step 5. Otherwise, proceed to Step 6.
5. The error is returned by the server containing information about the incorrectly supplied request with a status code of 403 Unauthorized.
6. The consultation update is sent to the database. If an error is encountered, it is thrown to Step 8. Otherwise, proceed to step 9.
7. The consultationId and the new consultation fields are sent to the database.
8. The error is returned by the server. This error remains vague as to not divulge DB information.
9. The updated consultation Document is sent to the client.
10. The response from server contains either an Error object or the updated consultation with a status code of 200.

#### DELETE CONSULTATION
1. The client submits a DELETE request. This DELETE request contains a JWT in the Header, and JSON as the body containing the consultationId.
2. The request is validated. In this case, the consultationId must be a string. If it fails validation, an error is thrown to Step 3. Otherwise, proceed to Step 4.
3. The error is returned by the server containing information about the incorrectly supplied request with a status code of 400 Bad Request.
4. The JWT is validated. If it fails validation, an error is thrown to Step 5. Otherwise, proceed to Step 6.
5. The error is returned by the server containing information about the incorrectly supplied request with a status code of 403 Unauthorized.
6. The consultationId is sent to the database with a delete request. If an error is encountered, it is thrown to Step 8. Otherwise, proceed to step 9.
7. The consultationId is sent to the database and the consultation Document is deleted.
8. The error is returned by the server. This error remains vague as to not divulge DB information.
9. The ID of the deleted consultation is returned.
10. The response from server contains either an Error object or the deleted consultation ID with a status code of 204.

<br>
<br>
<br>

-------------------
## User Stories
-------------------

- As a doctor, I want to create an account, so I can use the software.
- As a doctor, I want to log into my account, so I can keep my patient's information private.
- As a doctor, I want to create new patients so I can record their information.
- As a doctor, I want to record a consultation so I have a reference to everything we discussed.
- As a doctor, I want to see all my patients in one spot, so I can see our previous consultations.
- As a doctor, I want to see my patient's previous consultations in detail, so I can see what I previously prescribed them and what we discussed.
- As a doctor, I want to be able to update a consultation, so I can record further details if I've overlooked them in the session.
- As a doctor, I want to be able to delete consultations, so I can correct the record or remove accidents.

<br>

- As a patient, I want to be able to log into the site, so only I and my doctor can see the results of my consultations.
- As a patient, I want to be able to view previous consultations so I don't have to remember what we discussed.



-------------------
## Planning Methodology
-------------------

Trello has been chosen as the platform for keeping track of this project. 

The Trello board will utilise five lists in which to group tasks:
- Backlog
- In Dev
- For Review
- Dev Done
- Done

***Trello Flow***

1. Ascertain tasks to be completed, write tasks into the **Backlog** list. These tasks will be developed using user stories, which we then break down into discrete tasks that can be completed.
2. Task is picked from the **Backlog**, and put into **In Dev** once begun.
3. Once task is relatively done, transfer task into **For Review**, where task is reviewed by other team member, which may entail further discourse about the task. 
4. If task needs more work, goes back into **In Dev**. If the *development* aspects of the task are done, but the task still requires other additional work such as documentation etc., the task is moved to **Dev Done**, with an accompanying note indicating what else needs to be done. 
5. Tasks are only moved to the final **Done** list when all aspects of a task are complete. 


<br>


## Trello Images here later
-------------------
