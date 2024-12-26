# MONGO DB TASK

## Design database for Zen class programme

### Creating zen class database:

  
```json
  use zenClass
```

## 1.) users collection 

### Assume the batch has around 3 students.
  
  ```json
  db.users.insertMany([
  {
    _id: ObjectId("user_id1"),
    name: "Chowiya",
    email: "chowiya@example.com",
    batch: "Zen Class 2024",
    codekata_problems_solved: 150,
    attendance: [
      { date: new ISODate("2024-10-15"), status: "Present" },
      { date: new ISODate("2024-10-16"), status: "Absent" }
    ],
    tasks_submitted: [
      { task_id: ObjectId("task_id1"), date: new ISODate("2024-10-15") }
    ]
  },
  {
    _id: ObjectId("user_id2"),
    name: "Bharath",
    email: "bharath@example.com",
    batch: "Zen Class 2024",
    codekata_problems_solved: 80,
    attendance: [
      { date: new ISODate("2024-10-15"), status: "Absent" },
      { date: new ISODate("2024-10-16"), status: "Absent" }
    ],
    tasks_submitted: []
  },
  {
    _id: ObjectId("user_id3"),
    name: "Nirmala",
    email: "nimmy@example.com",
    batch: "Zen Class 2024",
    codekata_problems_solved: 200,
    attendance: [
      { date: new ISODate("2024-10-15"), status: "Present" },
      { date: new ISODate("2024-10-16"), status: "Present" }
    ],
    tasks_submitted: [
      { task_id: ObjectId("task_id2"), date: new ISODate("2024-10-16") }
    ]
  }]);
  ```

 ## 2.)Codekata collection:

```json
db.codekata.insertMany([
  { _id: ObjectId("codekata1"), user_id: ObjectId("user_id1"), problems_solved: 150 },
  { _id: ObjectId("codekata2"), user_id: ObjectId("user_id2"), problems_solved: 80 },
  { _id: ObjectId("codekata3"), user_id: ObjectId("user_id3"), problems_solved: 200 }]);
```

## 3.)Attendance collection:

```json
db.attendance.insertMany([
  { _id: ObjectId("attendance1"), user_id: ObjectId("user_id1"), date: new ISODate("2024-10-15"), status: "Present" },
  { _id: ObjectId("attendance2"), user_id: ObjectId("user_id1"), date: new ISODate("2024-10-16"), status: "Absent" },
  { _id: ObjectId("attendance3"), user_id: ObjectId("user_id2"), date: new ISODate("2024-10-15"), status: "Absent" },
  { _id: ObjectId("attendance4"), user_id: ObjectId("user_id3"), date: new ISODate("2024-10-15"), status: "Present" }
]);
```

## 4.) Topics collection:

```json
db.topics.insertMany([
  { _id: ObjectId("topic_id1"), topic_name: "Javascript", date: new ISODate("2024-10-12") },
  { _id: ObjectId("topic_id2"), topic_name: "React js", date: new ISODate("2024-10-18") }
]);
```

## 5.) Task collection:

```json
db.tasks.insertMany([
  { _id: ObjectId("task_id1"), task_name: "Expensive calculator using js", date: new ISODate("2024-10-15"), topic_id: ObjectId("topic_id1") },
  { _id: ObjectId("task_id2"), task_name: "Movie searching App using react-router-dom", date: new ISODate("2024-10-16"), topic_id: ObjectId("topic_id2") }
]);
```

## 6.) Company_drives collection:

```json
db.company_drives.insertMany([
  {
    _id: ObjectId("drive_id1"),
    company_name: "Zoho",
    date: new ISODate("2024-10-20"),
    students_appeared: [ObjectId("user_id1"), ObjectId("user_id3")]
  },
  {
    _id: ObjectId("drive_id2"),
    company_name: "TCS",
    date: new ISODate("2024-10-25"),
    students_appeared: [ObjectId("user_id2"), ObjectId("user_id3")]
  }
]);
```

## 7.) Mentors collection:

```json
db.mentors.insertMany([
  {
    _id: ObjectId("mentor_id1"),
    name: "Loganathan",
    mentees: [ObjectId("user_id1"), ObjectId("user_id2"), ObjectId("user_id3")],
    mentees_count: 3
  },
  {
    _id: ObjectId("mentor_id2"),
    name: "Jayanthi",
    mentees: [ObjectId("user_id2")],
    mentees_count: 1
  }
]);
```

# QUERIES

## 1.Find all the topics and tasks taught in the month of October

```json
db.topics.aggregate([
  {
    $match: {date: { $gte: new ISODate("2020-10-01"),$lte:new ISODate("2020-10-31") }
    }
   },
  {
    $lookup: {
      from: "tasks",
      localField: "_id",
      foreignField: "topic_id",
      as: "related_tasks"
    }
  },
  {
    $project: {
      topic_name: 1,
      date: 1,
      related_tasks: { task_name: 1, date: 1 }
    }
  }
]);

```


## 2. Find all the company drives which appeared between 15-Oct-2020 and 31-Oct-2020
```json
db.company_drives.find({
  date: { $gte: new ISODate("2020-10-15"), $lte: new ISODate("2020-10-31") }
});

```

## 3. Find all the company drives and students who appeared for the placement
```json
db.company_drives.aggregate([
  {
    $lookup: {
      from: "users",
      localField: "students_appeared",
      foreignField: "_id",
      as: "appeared_students"
    }
  },
  {
    $project: {
      company_name: 1,
      date: 1,
      appeared_students: { name: 1, email: 1 }
    }
  }
]);

```

## 4.Find the number of problems solved by each user in Codekata

```json
db.codekata.aggregate([
  {
    $lookup: {from: "users",localField: "user_id",foreignField: "_id",as: "user_details"}
  },
  {
    $project: {
      user_name: { $arrayElemAt: ["$user_details.name", 0] },
      email: { $arrayElemAt: ["$user_details.email", 0] },
      problems_solved: 1
    }
  }
]);

```

## 5.  Find all the mentors who have a mentee count of more than 15

```json
db.mentors.find({
  mentees_count: { $gt: 15 }
});

```

## 6.Find the number of users who are absent and tasks not submitted between 15-Oct-2020 and 31-Oct-2020

```json
db.users.aggregate([{
    $lookup: {
      from: "attendance",
      localField: "_id",
      foreignField: "user_id",
      as: "attendance_records"
    }},
  {
    $lookup: {from: "tasks",localField: "_id",foreignField: "user_id",as: "task_records"} 
    },
  {
    $match: {
      $and: [{
          "attendance_records.date": {
            $not: { $gte: new ISODate("2020-10-15"), $lte: new ISODate("2020-10-31") }
          }},
        {
          "task_records.date": {
            $not: { $gte: new ISODate("2020-10-15"), $lte: new ISODate("2020-10-31") }
          }}]
    }
  },
  {
    $count: "absent_users_with_no_tasks"
  }]);

```

