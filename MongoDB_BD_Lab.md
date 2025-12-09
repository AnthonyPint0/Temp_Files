# MongoDB CRUD Scripts

This Markdown file contains MongoDB shell (`mongosh`) scripts for four sample systems:

* **Student Academic Record Management** (`school`)
* **Employee Payroll Management** (`payroll_db`)
* **Banking System** (`banking_db`)
* **Insurance Management** (`insurance_db`)

Each section: switch to DB, create collections, insert 5 documents, read all documents, update one document, delete one document, and confirm deletions.

---

## 1) Student Academic Record Management (`school`)

```js
// switch to DB
use school;

// ---------------------------
// 1) Create collections
// ---------------------------
// (MongoDB will auto-create on insert; explicit creation included for clarity)
db.createCollection("students");
db.createCollection("courses");
db.createCollection("enrollments");

// ---------------------------
// 2) Insert 5 documents each
// ---------------------------

// students: _id as string student codes
db.students.insertMany([
  {
    _id: "S2024001",
    name: "Asha Verma",
    department: "CSE",
    year: 3,
    email: "asha@univ.edu",
    phone: "9876543210",
    created_at: ISODate("2024-07-01T10:00:00Z")
  },
  {
    _id: "S2024002",
    name: "Rohan Mehta",
    department: "ECE",
    year: 2,
    email: "rohan@univ.edu",
    phone: "9876501234",
    created_at: ISODate("2024-07-02T11:15:00Z")
  },
  {
    _id: "S2024003",
    name: "Priya Singh",
    department: "ME",
    year: 4,
    email: "priya@univ.edu",
    phone: "9876598765",
    created_at: ISODate("2024-07-03T09:30:00Z")
  },
  {
    _id: "S2024004",
    name: "Karan Patel",
    department: "CSE",
    year: 1,
    email: "karan@univ.edu",
    phone: "9876540000",
    created_at: ISODate("2024-07-04T08:00:00Z")
  },
  {
    _id: "S2024005",
    name: "Neha Reddy",
    department: "IT",
    year: 3,
    email: "neha@univ.edu",
    phone: "9876512345",
    created_at: ISODate("2024-07-05T12:00:00Z")
  }
]);

// courses: course codes as _id
db.courses.insertMany([
  { _id: "CSE201", title: "Data Structures", credits: 4, semester: 3 },
  { _id: "CSE202", title: "Algorithms", credits: 4, semester: 4 },
  { _id: "ECE101", title: "Circuits", credits: 3, semester: 1 },
  { _id: "ME301",  title: "Thermodynamics", credits: 3, semester: 5 },
  { _id: "IT210",  title: "Database Systems", credits: 4, semester: 3 }
]);

// enrollments: use ObjectId for each enrollment doc
db.enrollments.insertMany([
  {
    _id: ObjectId(),
    student_id: "S2024001",
    course_id: "CSE201",
    marks: 86,
    grade: "A",
    status: "Completed"
  },
  {
    _id: ObjectId(),
    student_id: "S2024002",
    course_id: "IT210",
    marks: 74,
    grade: "B",
    status: "Completed"
  },
  {
    _id: ObjectId(),
    student_id: "S2024003",
    course_id: "ME301",
    marks: 91,
    grade: "A+",
    status: "Completed"
  },
  {
    _id: ObjectId(),
    student_id: "S2024004",
    course_id: "ECE101",
    marks: 65,
    grade: "C",
    status: "Completed"
  },
  {
    _id: ObjectId(),
    student_id: "S2024005",
    course_id: "CSE202",
    marks: 79,
    grade: "B+",
    status: "Completed"
  }
]);

// ---------------------------
// 3) Read all documents in each collection
// ---------------------------

print("=== students ===");
db.students.find().pretty().forEach(doc => printjson(doc));

print("=== courses ===");
db.courses.find().pretty().forEach(doc => printjson(doc));

print("=== enrollments ===");
db.enrollments.find().pretty().forEach(doc => printjson(doc));

// ---------------------------
// 4) Update one document in each collection
// ---------------------------

// students: update phone and email for student S2024002
db.students.updateOne(
  { _id: "S2024002" },
  {
    $set: {
      phone: "9999900001",
      email: "rohan.mehta@univ.edu"
    },
    $currentDate: { last_modified: true }
  }
);

// courses: change credits for IT210 from 4 to 3 (example)
db.courses.updateOne(
  { _id: "IT210" },
  { $set: { credits: 3, title: "Database Systems (updated)" } }
);

// enrollments: bump marks for S2024004 in ECE101 from 65 to 72 and recompute grade
db.enrollments.updateOne(
  { student_id: "S2024004", course_id: "ECE101" },
  {
    $set: { marks: 72, grade: "B-" },
    $currentDate: { grade_updated_at: true }
  }
);

// show the updated docs to confirm
print("=== updated student S2024002 ===");
printjson(db.students.findOne({ _id: "S2024002" }));

print("=== updated course IT210 ===");
printjson(db.courses.findOne({ _id: "IT210" }));

print("=== updated enrollment for S2024004, ECE101 ===");
printjson(db.enrollments.findOne({ student_id: "S2024004", course_id: "ECE101" }));

// ---------------------------
// 5) Delete one document from each collection
// ---------------------------

// students: delete S2024005
db.students.deleteOne({ _id: "S2024005" });

// courses: delete ME301
db.courses.deleteOne({ _id: "ME301" });

// enrollments: delete the enrollment record for S2024003 in ME301
db.enrollments.deleteOne({ student_id: "S2024003", course_id: "ME301" });

// confirm deletions
print("=== students after deletion ===");
db.students.find().forEach(doc => printjson(doc));

print("=== courses after deletion ===");
db.courses.find().forEach(doc => printjson(doc));

print("=== enrollments after deletion ===");
db.enrollments.find().forEach(doc => printjson(doc));
```

---

## 2) Employee Payroll Management (`payroll_db`)

```js
// switch to DB
use payroll_db;

// Collections
db.createCollection("employees");
db.createCollection("payroll");
db.createCollection("attendance");

// Insert 5 employees
db.employees.insertMany([
  {
    _id: "E101",
    name: "Ravi Kumar",
    designation: "Developer",
    department: "IT",
    email: "ravi.kumar@company.com",
    phone: "9876500001",
    date_joined: ISODate("2022-03-15T09:00:00Z"),
    salary_structure: { basic: 40000, hra: 15000, allowances: 5000, deductions: 3000 }
  },
  {
    _id: "E102",
    name: "Sana Kapoor",
    designation: "QA Engineer",
    department: "QA",
    email: "sana.kapoor@company.com",
    phone: "9876500002",
    date_joined: ISODate("2021-11-01T09:30:00Z"),
    salary_structure: { basic: 35000, hra: 12000, allowances: 3000, deductions: 2500 }
  },
  {
    _id: "E103",
    name: "Arjun Singh",
    designation: "HR Executive",
    department: "HR",
    email: "arjun.singh@company.com",
    phone: "9876500003",
    date_joined: ISODate("2020-06-20T10:00:00Z"),
    salary_structure: { basic: 30000, hra: 10000, allowances: 2000, deductions: 2000 }
  },
  {
    _id: "E104",
    name: "Maya Iyer",
    designation: "Manager",
    department: "Sales",
    email: "maya.iyer@company.com",
    phone: "9876500004",
    date_joined: ISODate("2019-01-10T09:00:00Z"),
    salary_structure: { basic: 60000, hra: 20000, allowances: 8000, deductions: 5000 }
  },
  {
    _id: "E105",
    name: "Vikram Rao",
    designation: "Support Engineer",
    department: "Support",
    email: "vikram.rao@company.com",
    phone: "9876500005",
    date_joined: ISODate("2023-08-01T09:00:00Z"),
    salary_structure: { basic: 28000, hra: 9000, allowances: 1500, deductions: 1500 }
  }
]);

// Insert 5 payroll records (monthly)
db.payroll.insertMany([
  {
    _id: ObjectId(),
    emp_id: "E101",
    month: "2025-01",
    basic: 40000,
    hra: 15000,
    allowances: 5000,
    deductions: 3000,
    net_salary: 57000,
    generated_at: ISODate("2025-01-31T12:00:00Z")
  },
  {
    _id: ObjectId(),
    emp_id: "E102",
    month: "2025-01",
    basic: 35000,
    hra: 12000,
    allowances: 3000,
    deductions: 2500,
    net_salary: 47500,
    generated_at: ISODate("2025-01-31T12:05:00Z")
  },
  {
    _id: ObjectId(),
    emp_id: "E103",
    month: "2025-01",
    basic: 30000,
    hra: 10000,
    allowances: 2000,
    deductions: 2000,
    net_salary: 40000,
    generated_at: ISODate("2025-01-31T12:10:00Z")
  },
  {
    _id: ObjectId(),
    emp_id: "E104",
    month: "2025-01",
    basic: 60000,
    hra: 20000,
    allowances: 8000,
    deductions: 5000,
    net_salary: 83000,
    generated_at: ISODate("2025-01-31T12:15:00Z")
  },
  {
    _id: ObjectId(),
    emp_id: "E105",
    month: "2025-01",
    basic: 28000,
    hra: 9000,
    allowances: 1500,
    deductions: 1500,
    net_salary: 36000,
    generated_at: ISODate("2025-01-31T12:20:00Z")
  }
]);

// Insert 5 attendance records
db.attendance.insertMany([
  { _id: ObjectId(), emp_id: "E101", date: ISODate("2025-02-03T00:00:00Z"), status: "Present" },
  { _id: ObjectId(), emp_id: "E102", date: ISODate("2025-02-03T00:00:00Z"), status: "Present" },
  { _id: ObjectId(), emp_id: "E103", date: ISODate("2025-02-03T00:00:00Z"), status: "Leave" },
  { _id: ObjectId(), emp_id: "E104", date: ISODate("2025-02-03T00:00:00Z"), status: "Present" },
  { _id: ObjectId(), emp_id: "E105", date: ISODate("2025-02-03T00:00:00Z"), status: "Absent" }
]);

// Read all documents in each collection
print("=== employees ==="); db.employees.find().forEach(d => printjson(d));
print("=== payroll ==="); db.payroll.find().forEach(d => printjson(d));
print("=== attendance ==="); db.attendance.find().forEach(d => printjson(d));

// Update one document in each collection
// employees: promote E105 to Senior Support and bump basic
db.employees.updateOne(
  { _id: "E105" },
  { $set: { designation: "Senior Support Engineer", "salary_structure.basic": 32000 }, $currentDate: { updated_at: true } }
);

// payroll: correct net_salary for E103 (example)
db.payroll.updateOne(
  { emp_id: "E103", month: "2025-01" },
  { $set: { net_salary: 39500 }, $currentDate: { payroll_updated_at: true } }
);

// attendance: change E105 status from Absent to Present for that date
db.attendance.updateOne(
  { emp_id: "E105", date: ISODate("2025-02-03T00:00:00Z") },
  { $set: { status: "Present" }, $currentDate: { attendance_updated_at: true } }
);

// Show updated docs
print("=== updated employee E105 ==="); printjson(db.employees.findOne({ _id: "E105" }));
print("=== updated payroll E103 ==="); printjson(db.payroll.findOne({ emp_id: "E103", month: "2025-01" }));
print("=== updated attendance E105 ==="); printjson(db.attendance.findOne({ emp_id: "E105", date: ISODate("2025-02-03T00:00:00Z") }));

// Delete one document from each collection
db.employees.deleteOne({ _id: "E104" }); // somebody retired
db.payroll.deleteOne({ emp_id: "E104", month: "2025-01" });
db.attendance.deleteOne({ emp_id: "E103", date: ISODate("2025-02-03T00:00:00Z") });

// Confirm deletions
print("=== employees after deletion ==="); db.employees.find().forEach(d => printjson(d));
print("=== payroll after deletion ==="); db.payroll.find().forEach(d => printjson(d));
print("=== attendance after deletion ==="); db.attendance.find().forEach(d => printjson(d));
```

---

## 3) Banking System (`banking_db`)

```js
// switch to DB
use banking_db;

// Collections
db.createCollection("customers");
db.createCollection("accounts");
db.createCollection("transactions");

// Insert 5 customers
db.customers.insertMany([
  {
    _id: "C2001",
    name: "Meera Sharma",
    email: "meera.sharma@mail.com",
    phone: "9823900001",
    address: { line1: "12 Park St", city: "Mumbai", state: "MH", pincode: "400001" },
    created_at: ISODate("2020-05-10T09:00:00Z")
  },
  {
    _id: "C2002",
    name: "Amit Desai",
    email: "amit.desai@mail.com",
    phone: "9823900002",
    address: { line1: "44 MG Rd", city: "Pune", state: "MH", pincode: "411001" },
    created_at: ISODate("2021-08-12T09:30:00Z")
  },
  {
    _id: "C2003",
    name: "Leela Nair",
    email: "leela.nair@mail.com",
    phone: "9823900003",
    address: { line1: "7 Ocean Ave", city: "Chennai", state: "TN", pincode: "600001" },
    created_at: ISODate("2019-02-20T10:00:00Z")
  },
  {
    _id: "C2004",
    name: "Sunil Patil",
    email: "sunil.patil@mail.com",
    phone: "9823900004",
    address: { line1: "9 Hill Rd", city: "Mumbai", state: "MH", pincode: "400002" },
    created_at: ISODate("2022-01-03T09:00:00Z")
  },
  {
    _id: "C2005",
    name: "Divya Rao",
    email: "divya.rao@mail.com",
    phone: "9823900005",
    address: { line1: "2 Lake View", city: "Bengaluru", state: "KA", pincode: "560001" },
    created_at: ISODate("2023-07-18T09:00:00Z")
  }
]);

// Insert 5 accounts
db.accounts.insertMany([
  {
    _id: "ACC100001",
    customer_id: "C2001",
    type: "Savings",
    balance: 150000,
    status: "Active",
    opened_at: ISODate("2020-05-11T09:00:00Z")
  },
  {
    _id: "ACC100002",
    customer_id: "C2002",
    type: "Current",
    balance: 250000,
    status: "Active",
    opened_at: ISODate("2021-08-13T09:00:00Z")
  },
  {
    _id: "ACC100003",
    customer_id: "C2003",
    type: "Savings",
    balance: 50000,
    status: "Active",
    opened_at: ISODate("2019-02-21T10:00:00Z")
  },
  {
    _id: "ACC100004",
    customer_id: "C2004",
    type: "Savings",
    balance: 80000,
    status: "Active",
    opened_at: ISODate("2022-01-04T09:00:00Z")
  },
  {
    _id: "ACC100005",
    customer_id: "C2005",
    type: "Current",
    balance: 120000,
    status: "Active",
    opened_at: ISODate("2023-07-19T09:00:00Z")
  }
]);

// Insert 5 transactions
db.transactions.insertMany([
  { _id: ObjectId(), account_id: "ACC100001", type: "deposit", amount: 50000, timestamp: ISODate("2025-02-01T10:00:00Z"), description: "Salary" },
  { _id: ObjectId(), account_id: "ACC100002", type: "withdrawal", amount: 20000, timestamp: ISODate("2025-02-02T11:00:00Z"), description: "ATM withdrawal" },
  { _id: ObjectId(), account_id: "ACC100003", type: "deposit", amount: 10000, timestamp: ISODate("2025-02-03T12:00:00Z"), description: "Transfer received" },
  { _id: ObjectId(), account_id: "ACC100004", type: "transfer", amount: 5000, timestamp: ISODate("2025-02-04T13:00:00Z"), description: "Transfer to friend" },
  { _id: ObjectId(), account_id: "ACC100005", type: "deposit", amount: 20000, timestamp: ISODate("2025-02-05T14:00:00Z"), description: "Deposit" }
]);

// Read all documents
print("=== customers ==="); db.customers.find().forEach(d => printjson(d));
print("=== accounts ==="); db.accounts.find().forEach(d => printjson(d));
print("=== transactions ==="); db.transactions.find().forEach(d => printjson(d));

// Update one document in each collection
// customers: update phone for C2002
db.customers.updateOne({ _id: "C2002" }, { $set: { phone: "9900000002" }, $currentDate: { updated_at: true } });

// accounts: freeze account ACC100003 (status -> Frozen)
db.accounts.updateOne({ _id: "ACC100003" }, { $set: { status: "Frozen" } });

// transactions: annotate transaction description for ACC100004
db.transactions.updateOne(
  { account_id: "ACC100004", type: "transfer" },
  { $set: { description: "Transfer to friend (NEFT)", verified: true }, $currentDate: { txn_verified_at: true } }
);

// Show updated docs
print("=== updated customer C2002 ==="); printjson(db.customers.findOne({ _id: "C2002" }));
print("=== updated account ACC100003 ==="); printjson(db.accounts.findOne({ _id: "ACC100003" }));
print("=== updated transaction ACC100004 ==="); printjson(db.transactions.findOne({ account_id: "ACC100004", type: "transfer" }));

// Delete one document from each collection
db.customers.deleteOne({ _id: "C2005" });
db.accounts.deleteOne({ _id: "ACC100005" });
db.transactions.deleteOne({ account_id: "ACC100003", type: "deposit" }); // remove that particular deposit

// Confirm deletions
print("=== customers after deletion ==="); db.customers.find().forEach(d => printjson(d));
print("=== accounts after deletion ==="); db.accounts.find().forEach(d => printjson(d));
print("=== transactions after deletion ==="); db.transactions.find().forEach(d => printjson(d));
```

---

## 4) Insurance Management (`insurance_db`)

```js
// switch to DB
use insurance_db;

// Collections
db.createCollection("customers");
db.createCollection("policies");
db.createCollection("claims");

// Insert 5 customers
db.customers.insertMany([
  {
    _id: "CU4001",
    name: "Anjali Rao",
    email: "anjali.rao@client.com",
    phone: "9812300001",
    address: { city: "Chennai", state: "TN", pincode: "600001" }
  },
  {
    _id: "CU4002",
    name: "Ramesh B",
    email: "ramesh.b@client.com",
    phone: "9812300002",
    address: { city: "Hyderabad", state: "TS", pincode: "500001" }
  },
  {
    _id: "CU4003",
    name: "Geeta Patel",
    email: "geeta.patel@client.com",
    phone: "9812300003",
    address: { city: "Ahmedabad", state: "GJ", pincode: "380001" }
  },
  {
    _id: "CU4004",
    name: "Kunal Sharma",
    email: "kunal.sharma@client.com",
    phone: "9812300004",
    address: { city: "Delhi", state: "DL", pincode: "110001" }
  },
  {
    _id: "CU4005",
    name: "Sneha Gupta",
    email: "sneha.gupta@client.com",
    phone: "9812300005",
    address: { city: "Kolkata", state: "WB", pincode: "700001" }
  }
]);

// Insert 5 policies
db.policies.insertMany([
  {
    _id: "P5001",
    customer_id: "CU4001",
    policy_type: "Health",
    start_date: ISODate("2025-01-01T00:00:00Z"),
    end_date: ISODate("2026-01-01T00:00:00Z"),
    premium_amount: 12000,
    nominee: "Ravi Rao",
    status: "Active"
  },
  {
    _id: "P5002",
    customer_id: "CU4002",
    policy_type: "Life",
    start_date: ISODate("2024-06-01T00:00:00Z"),
    end_date: ISODate("2034-06-01T00:00:00Z"),
    premium_amount: 30000,
    nominee: "Seema B",
    status: "Active"
  },
  {
    _id: "P5003",
    customer_id: "CU4003",
    policy_type: "Auto",
    start_date: ISODate("2023-03-15T00:00:00Z"),
    end_date: ISODate("2026-03-14T00:00:00Z"),
    premium_amount: 8000,
    nominee: "Raj Patel",
    status: "Active"
  },
  {
    _id: "P5004",
    customer_id: "CU4004",
    policy_type: "Home",
    start_date: ISODate("2022-12-01T00:00:00Z"),
    end_date: ISODate("2027-12-01T00:00:00Z"),
    premium_amount: 18000,
    nominee: "Neha Sharma",
    status: "Active"
  },
  {
    _id: "P5005",
    customer_id: "CU4005",
    policy_type: "Health",
    start_date: ISODate("2024-09-01T00:00:00Z"),
    end_date: ISODate("2025-09-01T00:00:00Z"),
    premium_amount: 10000,
    nominee: "Amit Gupta",
    status: "Active"
  }
]);

// Insert 5 claims
db.claims.insertMany([
  {
    _id: "CL7001",
    policy_id: "P5001",
    claim_date: ISODate("2025-02-01T00:00:00Z"),
    amount: 75000,
    status: "Pending",
    description: "Hospitalization claim"
  },
  {
    _id: "CL7002",
    policy_id: "P5003",
    claim_date: ISODate("2024-11-10T00:00:00Z"),
    amount: 25000,
    status: "Approved",
    description: "Accident repair"
  },
  {
    _id: "CL7003",
    policy_id: "P5002",
    claim_date: ISODate("2025-01-20T00:00:00Z"),
    amount: 500000,
    status: "Pending",
    description: "Life benefit claim"
  },
  {
    _id: "CL7004",
    policy_id: "P5004",
    claim_date: ISODate("2023-12-15T00:00:00Z"),
    amount: 120000,
    status: "Rejected",
    description: "Home damage - investigation failed"
  },
  {
    _id: "CL7005",
    policy_id: "P5005",
    claim_date: ISODate("2025-02-03T00:00:00Z"),
    amount: 15000,
    status: "Pending",
    description: "OPD expenses"
  }
]);

// Read all documents
print("=== customers ==="); db.customers.find().forEach(d => printjson(d));
print("=== policies ==="); db.policies.find().forEach(d => printjson(d));
print("=== claims ==="); db.claims.find().forEach(d => printjson(d));

// Update one document in each collection
// customers: update email for CU4003
db.customers.updateOne({ _id: "CU4003" }, { $set: { email: "geeta.p@client.com" }, $currentDate: { updated_at: true } });

// policies: cancel P5005 (status -> Cancelled)
db.policies.updateOne({ _id: "P5005" }, { $set: { status: "Cancelled" }, $currentDate: { policy_cancelled_at: true } });

// claims: approve CL7001 and set settled_amount
db.claims.updateOne(
  { _id: "CL7001" },
  { $set: { status: "Approved", settled_amount: 70000, settlement_date: ISODate("2025-02-10T00:00:00Z") }, $currentDate: { claim_updated_at: true } }
);

// Show updated docs
print("=== updated customer CU4003 ==="); printjson(db.customers.findOne({ _id: "CU4003" }));
print("=== updated policy P5005 ==="); printjson(db.policies.findOne({ _id: "P5005" }));
print("=== updated claim CL7001 ==="); printjson(db.claims.findOne({ _id: "CL7001" }));

// Delete one document from each collection
db.customers.deleteOne({ _id: "CU4005" }); // remove that customer
db.policies.deleteOne({ _id: "P5004" });   // delete a policy
db.claims.deleteOne({ _id: "CL7004" });    // delete the rejected claim

// Confirm deletions
print("=== customers after deletion ==="); db.customers.find().forEach(d => printjson(d));
print("=== policies after deletion ==="); db.policies.find().forEach(d => printjson(d));
print("=== claims after deletion ==="); db.claims.find().forEach(d => printjson(d));
```
