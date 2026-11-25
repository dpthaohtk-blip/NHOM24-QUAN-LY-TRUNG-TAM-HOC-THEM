# Thiết kế Cơ sở dữ liệu & Sơ đồ ERD — Quản lý Trung tâm Học thêm (TCM-6)

## 1. Các bảng dữ liệu chính
- **USERS**: Tài khoản đăng nhập và phân quyền (`user_id` PK, `username`, `password_hash`, `full_name`, `email`, `phone`, `role`, `status`)
- **TEACHERS**: Hồ sơ giáo viên (`teacher_id` PK, `user_id` FK -> `USERS`, `specialization`, `hire_date`)
- **STUDENTS**: Hồ sơ học viên (`student_id` PK, `user_id` FK -> `USERS`, `full_name`, `date_of_birth`, `school_name`, `parent_name`, `parent_phone`)
- **COURSES**: Môn học / Khóa học (`course_id` PK, `course_code`, `course_name`, `subject`, `default_fee`)
- **CLASSES**: Lớp học thêm (`class_id` PK, `course_id` FK -> `COURSES`, `teacher_id` FK -> `TEACHERS`, `class_name`, `room_or_link`, `max_students`, `status`)
- **CLASS_SCHEDULES**: Lịch học theo tuần (`schedule_id` PK, `class_id` FK -> `CLASSES`, `day_of_week`, `start_time`, `end_time`)
- **ENROLLMENTS**: Đăng ký lớp học (`enrollment_id` PK, `student_id` FK -> `STUDENTS`, `class_id` FK -> `CLASSES`, `enrolled_date`, `status`)
- **ATTENDANCES**: Điểm danh buổi học (`attendance_id` PK, `enrollment_id` FK -> `ENROLLMENTS`, `session_date`, `status`, `note`)
- **TUITION_INVOICES**: Hóa đơn học phí (`invoice_id` PK, `enrollment_id` FK -> `ENROLLMENTS`, `billing_period`, `amount_due`, `amount_paid`, `due_date`, `status`)
- **PAYMENTS**: Lịch sử thanh toán (`payment_id` PK, `invoice_id` FK -> `TUITION_INVOICES`, `paid_amount`, `payment_method`, `paid_at`)

## 2. Mối quan hệ chính
- `USERS` (1) -- (0..1) `TEACHERS` và `USERS` (1) -- (0..1) `STUDENTS`
- `COURSES` (1) -- (N) `CLASSES`
- `TEACHERS` (1) -- (N) `CLASSES`
- `CLASSES` (1) -- (N) `CLASS_SCHEDULES`
- `STUDENTS` (1) -- (N) `ENROLLMENTS` (N) -- (1) `CLASSES`
- `ENROLLMENTS` (1) -- (N) `ATTENDANCES`
- `ENROLLMENTS` (1) -- (N) `TUITION_INVOICES` (1) -- (N) `PAYMENTS`

## 3. Sơ đồ ERD (Mermaid)
```mermaid
erDiagram
    USERS ||--o| TEACHERS : has_profile
    USERS ||--o| STUDENTS : has_profile
    COURSES ||--o{ CLASSES : offers
    TEACHERS ||--o{ CLASSES : teaches
    CLASSES ||--o{ CLASS_SCHEDULES : has_schedule
    STUDENTS ||--o{ ENROLLMENTS : enrolls
    CLASSES ||--o{ ENROLLMENTS : includes
    ENROLLMENTS ||--o{ ATTENDANCES : records
    ENROLLMENTS ||--o{ TUITION_INVOICES : bills
    TUITION_INVOICES ||--o{ PAYMENTS : receives

    USERS {
        int user_id PK
        string username
        string full_name
        string role
        string status
    }
    TEACHERS {
        int teacher_id PK
        int user_id FK
        string specialization
        date hire_date
    }
    STUDENTS {
        int student_id PK
        int user_id FK
        string full_name
        date date_of_birth
        string school_name
        string parent_phone
    }
    COURSES {
        int course_id PK
        string course_code
        string course_name
        decimal default_fee
    }
    CLASSES {
        int class_id PK
        int course_id FK
        int teacher_id FK
        string class_name
        string room_or_link
        int max_students
        string status
    }
    CLASS_SCHEDULES {
        int schedule_id PK
        int class_id FK
        int day_of_week
        string start_time
        string end_time
    }
    ENROLLMENTS {
        int enrollment_id PK
        int student_id FK
        int class_id FK
        date enrolled_date
        string status
    }
    ATTENDANCES {
        int attendance_id PK
        int enrollment_id FK
        date session_date
        string status
    }
    TUITION_INVOICES {
        int invoice_id PK
        int enrollment_id FK
        string billing_period
        decimal amount_due
        decimal amount_paid
        string status
    }
    PAYMENTS {
        int payment_id PK
        int invoice_id FK
        decimal paid_amount
        string payment_method
        datetime paid_at
    }