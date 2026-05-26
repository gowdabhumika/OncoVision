# OncoVision - System Architecture Documentation

## Table of Contents
1. [Overview](#overview)
2. [High-Level Architecture](#high-level-architecture)
3. [Component Architecture](#component-architecture)
4. [Data Flow](#data-flow)
5. [Technology Stack](#technology-stack)
6. [Database Schema](#database-schema)
7. [API Architecture](#api-architecture)
8. [Deployment Architecture](#deployment-architecture)
9. [Security Architecture](#security-architecture)

---

## Overview

OncoVision is a three-tier AI-powered healthcare platform designed to assist medical professionals in early cancer detection through intelligent analysis of medical imaging data. The system integrates modern web technologies with machine learning models to provide real-time predictions and patient management capabilities.

### System Goals
- Enable early cancer detection through AI-powered image analysis
- Provide secure, HIPAA-compliant medical data handling
- Deliver scalable, accessible healthcare solutions
- Support multiple cancer types and imaging modalities

---

## High-Level Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     CLIENT LAYER                             │
├──────────────────────────────┬──────────────────────────────┤
│   Web Application (React)    │   Mobile Application (Future) │
│   - Authentication UI        │   - Remote Upload            │
│   - Image Upload Interface   │   - Real-time Predictions    │
│   - Dashboard & Reports      │   - Doctor Consultations     │
└──────────────────┬───────────┴──────────────────────────────┘
                   │ REST APIs / WebSocket
                   ▼
┌─────────────────────────────────────────────────────────────┐
│                   API GATEWAY / BFF                          │
│              (Node.js + Express Server)                      │
├─────────────────────────────────────────────────────────────┤
│ - JWT Authentication & Authorization                         │
│ - Request Validation & Rate Limiting                         │
│ - Image Processing & Optimization                            │
│ - Error Handling & Logging                                   │
└──────────────────┬─────────────────────────────────────────┘
                   │
        ┌──────────┼──────────┐
        ▼          ▼          ▼
   ┌────────────┬──────────┬──────────────┐
   │  Business  │   AI/ML  │   Storage    │
   │   Logic    │ Services │  Services    │
   └────────────┴──────────┴──────────────┘
        │          │          │
        ▼          ▼          ▼
   ┌──────────────────────────────────┐
   │     DATABASE LAYER               │
   │ - MongoDB (Primary Data Store)   │
   │ - Redis (Caching & Sessions)     │
   └──────────────────────────────────┘
        │
        ▼
   ┌──────────────────────────────────┐
   │     EXTERNAL SERVICES            │
   │ - Cloud Storage (Cloudinary)     │
   │ - File Processing (Firebase)     │
   │ - Analytics Platform             │
   └──────────────────────────────────┘
```

---

## Component Architecture

### 1. Frontend Layer (React + TypeScript)

```
frontend/
├── public/
├── src/
│   ├── components/
│   │   ├── Auth/
│   │   │   ├── LoginForm
│   │   │   ├── RegistrationForm
│   │   │   └── ProtectedRoute
│   │   ├── Dashboard/
│   │   │   ├── PredictionResults
│   │   │   ├── PatientHistory
│   │   │   └── Analytics
│   │   ├── ImageUpload/
│   │   │   ├── DragDropZone
│   │   │   ├── ImagePreview
│   │   │   └── UploadProgress
│   │   ├── Reports/
│   │   │   ├── PredictionReport
│   │   │   ├── ComparisonView
│   │   │   └── ExportReport
│   │   └── Common/
│   │       ├── Header
│   │       ├── Navigation
│   │       └── Footer
│   ├── pages/
│   │   ├── Home
│   │   ├── Dashboard
│   │   ├── Upload
│   │   ├── PatientRecords
│   │   ├── Predictions
│   │   └── NotFound
│   ├── services/
│   │   ├── api.ts (API Client)
│   │   ├── auth.ts (Authentication)
│   │   ├── prediction.ts (Prediction Services)
│   │   └── storage.ts (File Storage)
│   ├── store/
│   │   ├── authSlice.ts (Redux)
│   │   ├── predictionSlice.ts
│   │   └── userSlice.ts
│   ├── hooks/
│   │   ├── useAuth
│   │   ├── usePrediction
│   │   └── useFetch
│   ├── utils/
│   │   ├── validators.ts
│   │   ├── formatters.ts
│   │   └── constants.ts
│   ├── styles/
│   │   └── tailwind config
│   └── App.tsx
├── package.json
└── tsconfig.json
```

### 2. Backend Layer (Node.js + Express + TypeScript)

```
backend/
├── src/
│   ├── config/
│   │   ├── database.ts (MongoDB Connection)
│   │   ├── environment.ts (ENV Variables)
│   │   └── server.ts (Server Config)
│   ├── middleware/
│   │   ├── auth.ts (JWT Verification)
│   │   ├── errorHandler.ts
│   │   ├── requestLogger.ts
│   │   ├── rateLimit.ts
│   │   └── validation.ts
│   ├── controllers/
│   │   ├── authController.ts
│   │   ├── patientController.ts
│   │   ├── predictionController.ts
│   │   ├── imageController.ts
│   │   └── reportController.ts
│   ├── services/
│   │   ├── authService.ts
│   │   ├── patientService.ts
│   │   ├── predictionService.ts
│   │   ├── imageProcessingService.ts
│   │   ├── storageService.ts (Cloudinary/Firebase)
│   │   └── emailService.ts
│   ├── models/
│   │   ├── User.ts (User Schema)
│   │   ├── Patient.ts (Patient Schema)
│   │   ├── Prediction.ts (Prediction Schema)
│   │   ├── MedicalImage.ts (Image Schema)
│   │   └── Report.ts (Report Schema)
│   ├── routes/
│   │   ├── auth.ts
│   │   ├── patients.ts
│   │   ├── predictions.ts
│   │   ├── images.ts
│   │   └── reports.ts
│   ├── utils/
│   │   ├── jwt.ts
│   │   ├── encryption.ts
│   │   ├── validators.ts
│   │   └── helpers.ts
│   ├── types/
│   │   ├── index.ts (TypeScript Types)
│   │   └── models.ts
│   └── server.ts (Express App Entry)
├── tests/
│   ├── unit/
│   ├── integration/
│   └── e2e/
├── package.json
└── tsconfig.json
```

### 3. AI/ML Model Layer (Python)

```
ai-model/
├── src/
│   ├── models/
│   │   ├── cancer_detector.py (Main Model)
│   │   ├── preprocessing.py
│   │   ├── postprocessing.py
│   │   └── ensemble_model.py
│   ├── data/
│   │   ├── loaders.py
│   │   ├── augmentation.py
│   │   └── normalization.py
│   ├── training/
│   │   ├── train.py
│   │   ├── evaluate.py
│   │   └── hyperparameters.py
│   ├── inference/
│   │   ├── predictor.py
│   │   ├── model_wrapper.py
│   │   └── batch_prediction.py
│   └── utils/
│       ├── logger.py
│       ├── config.py
│       └── metrics.py
├── models/
│   ├── lung_cancer_model.pkl
│   ├── breast_cancer_model.pkl
│   ├── brain_tumor_model.pkl
│   └── ensemble_model.pkl
├── app.py (Flask/FastAPI Server)
├── requirements.txt
└── README.md
```

---

## Data Flow

### 1. Image Upload & Prediction Flow

```
User Upload
    │
    ▼
Frontend Validation
    │
    ├─ File Type Check
    ├─ File Size Check
    └─ Image Format Verification
    │
    ▼
Upload to Backend
    │
    ├─ JWT Authentication
    └─ Request Validation
    │
    ▼
Backend Processing
    │
    ├─ Save Metadata to DB
    ├─ Upload to Cloud Storage (Cloudinary)
    └─ Retrieve Secure URL
    │
    ▼
AI Model Queue
    │
    ├─ Image Preprocessing
    │   ├─ Resize & Normalize
    │   ├─ Noise Reduction
    │   └─ Contrast Enhancement
    │
    ▼
Model Inference
    │
    ├─ Run through CNN Model
    ├─ Get Probability Score
    └─ Generate Confidence Metrics
    │
    ▼
Result Storage
    │
    ├─ Save Prediction to MongoDB
    ├─ Store Confidence Scores
    ├─ Log Processing Time
    └─ Update Patient Record
    │
    ▼
Response to Frontend
    │
    ├─ Return Prediction Results
    ├─ Display Probability Scores
    └─ Generate Report PDF
```

### 2. User Authentication Flow

```
User Login
    │
    ▼
Submit Credentials
    │
    ▼
Backend Validation
    │
    ├─ Check Email/Username Exists
    ├─ Verify Password Hash
    └─ Check Account Status
    │
    ▼
Generate JWT Token
    │
    ├─ Encode User ID
    ├─ Set Expiration (24h)
    └─ Sign with Secret Key
    │
    ▼
Return Response
    │
    ├─ Access Token
    ├─ Refresh Token
    └─ User Profile Data
    │
    ▼
Frontend Storage
    │
    ├─ Save JWT in LocalStorage/SessionStorage
    └─ Set Authorization Headers
```

### 3. Patient Record Management Flow

```
Doctor/Patient Action
    │
    ▼
Backend Request
    │
    ├─ Verify JWT Token
    ├─ Check Permissions
    └─ Validate Request Body
    │
    ▼
Database Operation
    │
    ├─ Query Patient Data
    ├─ Retrieve Associated Images
    └─ Fetch Prediction History
    │
    ▼
Data Transformation
    │
    ├─ Format Response
    ├─ Aggregate Statistics
    └─ Remove Sensitive Fields
    │
    ▼
Return to Frontend
    │
    ├─ Display Patient Info
    ├─ Show Image History
    └─ Render Prediction Timeline
```

---

## Technology Stack

### Frontend
| Technology | Purpose |
|-----------|---------|
| **React 18** | UI Framework with hooks |
| **TypeScript** | Type-safe JavaScript |
| **Tailwind CSS** | Utility-first CSS framework |
| **Redux Toolkit** | State management |
| **Axios** | HTTP client for API calls |
| **React Router v6** | Client-side routing |
| **Formik** | Form state management |
| **Chart.js / D3.js** | Data visualization |
| **Vite** | Build tool & dev server |

### Backend
| Technology | Purpose |
|-----------|---------|
| **Node.js** | Runtime environment |
| **Express.js** | Web framework |
| **TypeScript** | Type-safe JavaScript |
| **MongoDB** | NoSQL database |
| **Mongoose** | MongoDB ODM |
| **JWT** | Authentication tokens |
| **Bcrypt** | Password hashing |
| **Multer** | File upload middleware |
| **Socket.io** | Real-time communication |
| **Nodemailer** | Email notifications |

### AI/ML
| Technology | Purpose |
|-----------|---------|
| **Python 3.9+** | ML programming language |
| **TensorFlow / Keras** | Deep learning framework |
| **PyTorch** | Alternative ML framework |
| **OpenCV** | Image processing |
| **Scikit-learn** | Traditional ML algorithms |
| **NumPy / Pandas** | Data manipulation |
| **Flask / FastAPI** | Model serving API |
| **ONNX** | Model interoperability |

### Database & Cache
| Technology | Purpose |
|-----------|---------|
| **MongoDB** | Primary data store |
| **Redis** | Caching & session management |
| **Mongoose** | Schema validation |

### External Services
| Service | Purpose |
|---------|---------|
| **Cloudinary** | Image storage & optimization |
| **Firebase Storage** | Alternative cloud storage |
| **SendGrid / AWS SES** | Email service |
| **Stripe / Razorpay** | Payment processing (future) |

### DevOps & Deployment
| Tool | Purpose |
|-----|---------|
| **Docker** | Containerization |
| **Vercel** | Frontend deployment |
| **Render / Railway** | Backend deployment |
| **GitHub Actions** | CI/CD pipeline |
| **PM2** | Process management |

---

## Database Schema

### Users Collection
```typescript
{
  _id: ObjectId,
  email: string (unique),
  username: string (unique),
  password: string (hashed),
  fullName: string,
  role: "doctor" | "patient" | "admin",
  specialization: string, // for doctors
  licenseNumber: string, // for doctors
  phoneNumber: string,
  profileImage: string (URL),
  organization: string,
  isActive: boolean,
  emailVerified: boolean,
  twoFactorEnabled: boolean,
  lastLogin: Date,
  createdAt: Date,
  updatedAt: Date,
  preferences: {
    notifications: boolean,
    theme: "light" | "dark",
    language: string
  }
}
```

### Patients Collection
```typescript
{
  _id: ObjectId,
  userId: ObjectId (ref: Users),
  medicalHistory: {
    conditions: string[],
    allergies: string[],
    medications: string[],
    surgeries: string[]
  },
  demographics: {
    age: number,
    gender: "M" | "F" | "Other",
    bloodType: string,
    height: number,
    weight: number
  },
  emergencyContact: {
    name: string,
    relationship: string,
    phone: string
  },
  assignedDoctors: ObjectId[], // ref: Users
  createdAt: Date,
  updatedAt: Date
}
```

### MedicalImages Collection
```typescript
{
  _id: ObjectId,
  patientId: ObjectId (ref: Patients),
  uploadedBy: ObjectId (ref: Users),
  imageType: "MRI" | "CT" | "XRay" | "Ultrasound",
  bodyPart: string,
  imageUrl: string (Cloudinary URL),
  thumbnailUrl: string,
  fileSize: number,
  metadata: {
    hospitalName: string,
    scanDate: Date,
    equipmentType: string,
    technician: string
  },
  processingStatus: "pending" | "processing" | "completed" | "failed",
  isQCPassed: boolean,
  createdAt: Date,
  updatedAt: Date
}
```

### Predictions Collection
```typescript
{
  _id: ObjectId,
  imageId: ObjectId (ref: MedicalImages),
  patientId: ObjectId (ref: Patients),
  modelVersion: string,
  cancerType: "lung" | "breast" | "brain" | "skin" | "oral",
  predictionResult: {
    classification: "malignant" | "benign" | "normal",
    confidenceScore: number (0-1),
    probability: {
      malignant: number,
      benign: number,
      normal: number
    }
  },
  flaggedAreas: {
    x: number,
    y: number,
    width: number,
    height: number,
    confidence: number
  }[],
  reviewedBy: ObjectId (ref: Users), // doctor
  reviewNotes: string,
  reviewStatus: "pending" | "reviewed" | "approved",
  processingTime: number (milliseconds),
  createdAt: Date,
  updatedAt: Date
}
```

### Reports Collection
```typescript
{
  _id: ObjectId,
  predictionId: ObjectId (ref: Predictions),
  patientId: ObjectId (ref: Patients),
  doctorId: ObjectId (ref: Users),
  reportContent: string (HTML/Markdown),
  recommendations: string[],
  followUpRequired: boolean,
  followUpDate: Date,
  exportFormat: "PDF" | "HTML",
  exportedAt: Date,
  isArchived: boolean,
  createdAt: Date,
  updatedAt: Date
}
```

### AuditLogs Collection
```typescript
{
  _id: ObjectId,
  userId: ObjectId (ref: Users),
  action: string,
  resourceType: string,
  resourceId: ObjectId,
  changes: {
    before: any,
    after: any
  },
  ipAddress: string,
  userAgent: string,
  createdAt: Date
}
```

---

## API Architecture

### Authentication Endpoints
```
POST /api/v1/auth/register
POST /api/v1/auth/login
POST /api/v1/auth/logout
POST /api/v1/auth/refresh-token
POST /api/v1/auth/forgot-password
POST /api/v1/auth/reset-password
GET  /api/v1/auth/verify-email/:token
```

### Patient Endpoints
```
GET    /api/v1/patients/:id
POST   /api/v1/patients
PUT    /api/v1/patients/:id
DELETE /api/v1/patients/:id
GET    /api/v1/patients/:id/history
GET    /api/v1/patients/:id/predictions
```

### Image Upload Endpoints
```
POST   /api/v1/images/upload
GET    /api/v1/images/:id
DELETE /api/v1/images/:id
GET    /api/v1/images/patient/:patientId
POST   /api/v1/images/:id/process
```

### Prediction Endpoints
```
GET    /api/v1/predictions/:id
GET    /api/v1/predictions/patient/:patientId
POST   /api/v1/predictions/batch
GET    /api/v1/predictions/:id/compare
POST   /api/v1/predictions/:id/review
```

### Report Endpoints
```
GET    /api/v1/reports/:id
POST   /api/v1/reports/:predictionId/generate
GET    /api/v1/reports/:id/download
POST   /api/v1/reports/:id/share
```

### Admin Endpoints
```
GET    /api/v1/admin/analytics
GET    /api/v1/admin/users
POST   /api/v1/admin/users/:id/deactivate
GET    /api/v1/admin/audit-logs
```

---

## Deployment Architecture

### Frontend Deployment (Vercel)
```
GitHub Repository
    │
    ▼
GitHub Actions (CI)
    │
    ├─ Lint Code
    ├─ Type Check
    ├─ Run Tests
    └─ Build
    │
    ▼
Vercel Deployment
    │
    ├─ Build Optimization
    ├─ Image Optimization
    ├─ CDN Distribution
    └─ SSL Certificate
    │
    ▼
Production (vercel.app)
```

### Backend Deployment (Render/Railway)
```
GitHub Repository
    │
    ▼
GitHub Actions (CI/CD)
    │
    ├─ Lint & Format Check
    ├─ TypeScript Compilation
    ├─ Unit Tests
    ├─ Integration Tests
    └─ Build Docker Image
    │
    ▼
Container Registry
    │
    ▼
Render / Railway
    │
    ├─ Environment Variables
    ├─ Secret Management
    ├─ Auto-scaling
    └─ Health Checks
    │
    ▼
Production Server
    │
    ├─ Node.js Runtime
    ├─ MongoDB Connection
    └─ Redis Cache
```

### Docker Architecture
```
Frontend Dockerfile
├─ Build Stage
│  ├─ Install dependencies
│  ├─ Build React app
│  └─ Create optimized bundle
└─ Serve Stage
   ├─ Nginx server
   └─ Serve static files

Backend Dockerfile
├─ Base Image: Node 18-alpine
├─ Install dependencies
├─ Compile TypeScript
├─ Expose port 5000
└─ Start application

ML Model Server Dockerfile
├─ Base Image: Python 3.9
├─ Install ML libraries
├─ Load trained models
├─ Expose port 8000
└─ Start Flask/FastAPI
```

---

## Security Architecture

### 1. Authentication & Authorization
```
Client Request
    │
    ▼
Extract JWT from Header
    │
    ▼
Verify JWT Signature
    │
    ├─ Check Expiration
    ├─ Validate Secret Key
    └─ Extract User ID
    │
    ▼
Check User Role & Permissions
    │
    ├─ Doctor: Full access to assigned patients
    ├─ Patient: Access only own data
    └─ Admin: System-wide access
    │
    ▼
Proceed / Deny Request
```

### 2. Data Protection
- **Encryption in Transit**: HTTPS/TLS 1.3
- **Encryption at Rest**: MongoDB encryption, sensitive fields hashed
- **Password Hashing**: bcrypt with salt rounds (10+)
- **API Key Rotation**: Automatic expiration and refresh

### 3. Security Headers
```
Content-Security-Policy: Prevent XSS attacks
X-Frame-Options: Prevent clickjacking
X-Content-Type-Options: Prevent MIME sniffing
Strict-Transport-Security: Force HTTPS
Access-Control-Allow-Origin: CORS policy
```

### 4. Rate Limiting
```
/api/auth/login: 5 attempts per 15 minutes
/api/images/upload: 100 requests per hour
/api/predictions: 50 requests per hour
Other endpoints: 1000 requests per hour
```

### 5. HIPAA Compliance
- Audit logging for all data access
- Data encryption (AES-256)
- Secure patient data handling
- Regular security audits
- Penetration testing

### 6. File Upload Security
```
Validation Steps:
├─ File Type Check (whitelist)
├─ File Size Limit (50MB)
├─ Virus Scan
├─ Metadata Stripping
└─ Secure Storage

Allowed Types:
├─ .jpg / .jpeg
├─ .png
├─ .dcm (DICOM)
└─ .tiff (medical imaging)
```

---

## Scalability Considerations

### Horizontal Scaling
- **Load Balancer**: Distribute traffic across multiple backend instances
- **Database Replication**: MongoDB replica sets
- **Redis Cluster**: Cache layer for session management
- **CDN**: Content delivery for frontend assets

### Vertical Scaling
- Increase server resources (CPU, RAM)
- Database indexing optimization
- Query optimization

### Caching Strategy
```
Layer 1: CDN Cache (Frontend assets)
    │
Layer 2: Redis Cache (API responses, sessions)
    │
Layer 3: Database Cache (MongoDB indexes)
```

### Queue System (Future Enhancement)
```
Task Queue (Bull/RabbitMQ)
    ├─ Image preprocessing
    ├─ Model inference
    ├─ Report generation
    └─ Email notifications

Worker Processes
    ├─ AI Model Processing
    ├─ PDF Generation
    └─ Email Service
```

---

## Performance Metrics

### Frontend
- Lighthouse Score: >90
- First Contentful Paint (FCP): <1.5s
- Largest Contentful Paint (LCP): <2.5s
- Cumulative Layout Shift (CLS): <0.1

### Backend
- API Response Time: <200ms (p95)
- Image Upload: <5s
- Model Inference: <10s
- Database Query: <100ms (p95)

### Infrastructure
- Uptime: >99.9%
- Database Availability: >99.99%
- Cache Hit Rate: >80%

---

## Monitoring & Logging

### Logging Tools
- **Frontend**: Sentry, LogRocket
- **Backend**: Winston, Bunyan
- **Infrastructure**: DataDog, New Relic

### Metrics to Monitor
```
Application Metrics:
├─ Request rate & latency
├─ Error rate & types
├─ API endpoint performance
└─ Model inference accuracy

Infrastructure Metrics:
├─ CPU & Memory usage
├─ Disk I/O
├─ Network bandwidth
└─ Database connections

Business Metrics:
├─ User registrations
├─ Daily active users
├─ Prediction accuracy
└─ Report generation time
```

---

## Disaster Recovery

### Backup Strategy
- Daily database backups to cloud storage
- Backup retention: 30 days
- Weekly full backup + daily incremental
- Test restore procedures monthly

### Failover Plan
- Automated failover to standby database
- Health checks every 30 seconds
- RTO (Recovery Time Objective): <5 minutes
- RPO (Recovery Point Objective): <1 minute

### Business Continuity
- Multi-region deployment (future)
- Active-passive database replication
- Incident response team
- Regular disaster recovery drills

---

## Future Architecture Enhancements

1. **Microservices Architecture**: Separate services for auth, prediction, reporting
2. **GraphQL API**: Alongside REST for flexible queries
3. **Mobile Applications**: Native iOS/Android apps
4. **Multi-Tenancy**: Support for multiple hospitals/clinics
5. **Advanced Analytics**: ML-powered insights and patterns
6. **Integration APIs**: EHR/HIS system integration
7. **Blockchain**: For medical data immutability
8. **Edge Computing**: On-device model inference for mobile

---

## References & Standards

- HIPAA Compliance Guidelines
- DICOM Standard (Medical Imaging)
- REST API Best Practices (RFC 7231)
- MongoDB Performance Optimization
- Express.js Security Best Practices
- React Performance Guidelines

---

**Document Version**: 1.0  
**Last Updated**: May 2026  
**Maintained By**: OncoVision Team
