sequenceDiagram
    autonumber
    actor Applicant as Candidate / Student
    participant Landing as recruitment/index-open.html
    participant Handler as recruitment/formsubmitted.php
    participant Mail as PHPMailer (SMTP)
    participant DB as theadast_recruitment (MySQL)
    participant ExamLogin as exam/login.php & loginDetails.php
    participant ExamSection as exam/php/<Section>/
    participant Admin as admin/php/retrieveExamDetails.php

    Note over Applicant,Landing: Stage 1: Application
    Applicant->>Landing: Browses sections (WebDev, Layout, Photo, etc.)
    Applicant->>Landing: Fills form (Bio, 1st & 2nd choice, exam date, uploads resume)
    Landing->>Handler: AJAX POST application data & resume file
    Handler->>DB: Deduplication check (ID_Number in 2025_3T_recruitment)
    Handler->>Handler: Generates passwords (e.g., 12100000_WEBDEV_3T_482)
    Handler->>DB: INSERT into 2025_3T_recruitment & examinees
    Handler->>Mail: Sends confirmation email with exam date & credentials
    Handler-->>Landing: 200 OK -> Redirect to confirmation.html

    Note over Applicant,ExamLogin: Stage 2: Scheduled Examination
    Applicant->>ExamLogin: Enters Student No & generated password
    ExamLogin->>DB: Validates credentials, date check (examDate == today), & status != 'Taken'
    ExamLogin->>DB: Initializes exam_details (timeRemaining: 5400s)
    ExamLogin-->>Applicant: Redirects to section test (e.g. php/Web Development/firstSection.php)

    Note over Applicant,ExamSection: Stage 3: Taking the Test
    Applicant->>ExamSection: Completes Multiple Choice, Identification & Essays
    ExamSection->>DB: Autosaves answers to answers table (auto-scores MC/ID against CorrectAnswer)
    Applicant->>ExamSection: Submits final exam (or timer expires)
    ExamSection->>DB: SUM(score) saved to exam_details, status = 'Taken'
    ExamSection-->>Applicant: Shows finished.html

    Note over Admin,DB: Stage 4: Review & Grading
    Admin->>DB: Reads exam scores, answers, and resumes
    Admin->>Admin: Manually grades essays & evaluates pass/fail (>= 70%)
