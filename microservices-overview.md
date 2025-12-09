
# # **Microservices Architecture Overview**

The **AI Proctored Examination System** uses a fully modular and scalable **microservices architecture** built with FastAPI, Redis, MySQL, MongoDB, RabbitMQ, and Nginx.

The backend is split into **8 core microservices** + **2 supporting services**, each responsible for its own domain and capable of independent scaling, deployment, and monitoring.

---

# ✅ **1. Microservices Breakdown**

Below is the enterprise-level microservices separation.

---

## 🟦 **1. Authentication & User Management Service**

### **Responsibilities**

* Login / Register / Forgot Password
* Face-authentication integration
* Role-based access & authorization
* Single active session enforcement

### **Tech Stack**

* FastAPI
* JWT + OAuth2
* bcrypt
* MySQL

### **Data Stored**

* Users
* Roles
* User-role mapping
* Face embedding references

### **Database**

* **MySQL**

---

## 🟦 **2. Exam Management Service**

### **Responsibilities**

* Create and edit exams
* Schedule exams
* Manage metadata for objective, subjective & practical exams
* Timer persistence
* Randomization rules
* Result calculation

### **Tech Stack**

* FastAPI
* SQLAlchemy
* MySQL

### **Data Stored**

* Exams
* Schedules
* Exam metadata
* Time logs

### **Database**

* **MySQL**

---

## 🟦 **3. Question Bank & AI Question Generation Service**

### **Responsibilities**

* CRUD operations for question bank
* AI-powered generation of MCQs, subjective questions & practical prompts
* Tagging & difficulty labeling

### **Tech Stack**

* FastAPI
* OpenAI / Local LLM
* MySQL

### **Data Stored**

* Questions
* Generated questions
* AI metadata

### **Database**

* **MySQL**

---

## 🟦 **4. Student Exam Engine (Answer Submission Service)**

### **Responsibilities**

* Save objective answers
* Auto-save subjective answers
* Store practical code responses
* Maintain timer state across refresh
* Handle exam logic per student

### **Tech Stack**

* FastAPI
* Redis (autosave/cache)
* MySQL

### **Data Stored**

* Student responses
* Autosaved states
* Practical submissions

### **Database**

* **MySQL + Redis**

---

## 🟦 **5. Proctoring Engine (AI Monitoring Service)**

➡️ *Most critical and ML-heavy microservice*

### **Responsibilities**

* Face recognition
* Multiple-person detection
* Phone detection
* Gaze estimation
* Audio anomaly detection
* Capture images every 5 seconds
* Track tab/window switches
* Generate AI suspicion score

### **Tech Stack**

* FastAPI
* OpenCV
* MediaPipe
* YOLOv8/YOLOv11
* PyTorch / ONNX
* GPU-enabled server
* MongoDB
* MinIO/S3

### **Data Stored**

* Proctoring logs (gaze, phone, faces, events)
* Alerts
* Frame metadata
* AI scoring results
* Captured image URLs

### **Database**

* **MongoDB** (logs)
* **MinIO/S3** (image blobs)

---

## 🟦 **6. Coding Judge Service**

### **Responsibilities**

* Send code to SphereEngine
* Fetch results, compilation errors, test-case status
* Compute marks based on % test-cases passed

### **Tech Stack**

* FastAPI
* SphereEngine API

### **Data Stored**

* Code submissions
* Result status
* Marks
* Test-case pass/fail data

### **Database**

* **MySQL**

---

## 🟦 **7. Notification & Communication Service**

### **Responsibilities**

* Send exam notifications
* Send reminders
* Handle support ticket responses
* Email alerts for proctor violations

### **Tech Stack**

* FastAPI
* SMTP Provider / Twilio
* Celery (optional async)

### **Data Stored**

* Email logs
* Notification history

### **Database**

* **MySQL**

---

## 🟦 **8. API Documentation Service**

### **Responsibilities**

* Fetch OpenAPI specs from every microservice
* Merge them into **one unified API documentation**
* Serve consolidated Swagger UI

### **Tech Stack**

* FastAPI
* Swagger UI
* `openapi-merge` or custom script

---

# 🎯 **Supporting Services**

---

## 🟩 **A. API Gateway**

Handles all external requests and routes them to the respective microservices.

### **Features**

* Single public entrypoint
* URL routing
* Rate limiting
* SSL termination
* Security headers

### **Tech**

* **Nginx**

---

## 🟩 **B. Message Queue**

Used for asynchronous operations.

### **Used For**

* Proctoring events processing
* AI model analysis
* Email notifications
* Large async workloads

### **Tech**

* **RabbitMQ** (recommended)
* Kafka (optional alternative)

---

# 📌 **2. MySQL vs MongoDB — What Goes Where?**

---

## 🟦 **MySQL (Structured, transactional data)**

### **Stored In MySQL**

* Users
* Roles
* Exams
* Schedules
* Questions
* Objective/Subjective answers
* Practical submissions
* Exam results
* Notifications
* AI summary scores (proctor_scores)

**Reason:**
MySQL ensures ACID consistency and relational integrity — ideal for business logic.

---

## 🟩 **MongoDB (High-volume logs, flexible structure)**

### **Stored In MongoDB**

* Proctoring frame metadata
* Gaze detection logs
* Audio logs
* Tab switch logs
* Phone/person detection logs
* Raw AI analysis logs

**Reason:**
Designed for huge quantities of semi-structured JSON events.

---

## 🟫 **Object Storage (MinIO/S3)**

### **Stored In S3**

* Actual captured images
* Snapshot frames
* Video (if added later)

**Reason:**
Optimized for low-cost, scalable blob storage.

---

# 📁 **3. Code Repository Structure**

A **monorepo** is preferred for clean DevOps and versioning.

```
backend/
   auth-service/
   exam-management-service/
   question-generation-service/
   exam-engine/
   proctoring-service/
   code-judge-service/
   notification-service/
   gateway/
   api-docs-service/
   common-libs/
docker-compose.yml
```

### **Benefits**

* Shared utilities
* Easier CI/CD
* Cleaner version management
* One-command startup for all services

---

# 🚀 **Final Architecture Summary**

| Microservice           | Tech                   | DB           |
| ---------------------- | ---------------------- | ------------ |
| Auth Service           | FastAPI                | MySQL        |
| Exam Management        | FastAPI                | MySQL        |
| Question Bank & AI Gen | FastAPI + LLM          | MySQL        |
| Student Exam Engine    | FastAPI + Redis        | MySQL        |
| Proctoring Engine      | FastAPI + ML Models    | MongoDB + S3 |
| Coding Judge           | FastAPI + SphereEngine | MySQL        |
| Notification Service   | FastAPI                | MySQL        |
| API Docs Service       | FastAPI + Swagger      | —            |
| Gateway                | Nginx                  | —            |
| Queue                  | RabbitMQ               | —            |

---

