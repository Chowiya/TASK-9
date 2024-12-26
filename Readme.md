# MONGO DB TASK

## Design database for Zen class programme

### Creating zen class database:

  
```json
  use zenClass 
```

## 1.) users collection 

### Assume the batch has around 3 students.
  
  ```json
  db.users.insertMany(
  [
  { "_id": 1, "name": "Chowiya", "email": "chowiya@example.com" },
  { "_id": 2, "name": "Bharath", "email": "bharath@example.com" },
  { "_id": 3, "name": "Nirmala", "email": "nimmy@example.com" }
  ])
  ```

 ## 2.)Codekata collection:

```json
db.codekata.insertMany(
[
  { "_id": 1, "user_id": 1, "problems_solved": 120 },
  { "_id": 2, "user_id": 2, "problems_solved": 80 },
  { "_id": 3, "user_id": 3, "problems_solved": 150 }
])
```

## 3.)Attendance collection:

```json
db.attendance.insertMany(
[
  { "_id": 1, "user_id": 1, "date": "2020-10-15", "status": "Present" },
  { "_id": 2, "user_id": 2, "date": "2020-10-16", "status": "Absent" },
  { "_id": 3, "user_id": 3, "date": "2020-10-17", "status": "Absent" }
])
```

## 4.) Topics collection:

```json
db.topics.insertMany(
[
  { "_id": 1, "name": "JavaScript", "date": "2020-10-05" },
  { "_id": 2, "name": "React", "date": "2020-10-15" }
])
```

## 5.) Task collection:

```json
db.tasks.insertMany(
[
  { "_id": 1, "name": "JavaScript", "date": "2020-10-05" },
  { "_id": 2, "name": "React", "date": "2020-10-15" }
])
```

## 6.) Company_drives collection:

```json
db.company_drives.insertMany(
[
  { "_id": 1, "company_name": "Google", "date": "2020-10-20", "students_appeared": [1, 2] },
  { "_id": 2, "company_name": "Amazon", "date": "2020-10-25", "students_appeared": [2, 3] }
])
```

## 7.) Mentors collection:

```json
db.mentors.insertMany(
[
  { "_id": 1, "name": "Mentor A", "mentees_count": 10 },
  { "_id": 2, "name": "Mentor B", "mentees_count": 20 }
])
```

# QUERIES

## 1.Find all the topics and tasks taught in the month of October

- To find all Topic
```json

db.topics.find();

```
- To find task in October
```json
db.tasks.find({ date: { $gte: "2020-10-01", $lte: "2020-10-31" } })

```



## 2. Find all the company drives which appeared between 15-Oct-2020 and 31-Oct-2020
```json
db.company_drives.find({ date: { $gte: "2020-10-15", $lte: "2020-10-31" } })


```

## 3. Find all the company drives and students who appeared for the placement

- To find all company drives
```json
db.company_drives.find();

```
- To find student who appeared for placement
```json
db.users.find({ _id: { $in: [1, 2, 3] } });

```

## 4.Find the number of problems solved by each user in Codekata

```json
db.codekata.aggregate([
  {
    $lookup: {
      from: "users",                
      localField: "user_id",        
      foreignField: "_id",          
      as: "user_details"               }
  },
  {
    $project: {
      user_name: { $arrayElemAt: ["$user_details.name", 0] }, 
      email: { $arrayElemAt: ["$user_details.email", 0] },    
      problems_solved: 1                                      
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
db.attendance.aggregate([
  {$match: {date: { $gte: ISODate("2020-10-15"), $lte: ISODate("2020-10-31") },status: "Absent"}},
  {
    $lookup: {
      from: "tasks",
      localField: "user_id",
      foreignField: "user_id",
      as: "task_submission"
    }
  },
  {
    $match: {
      "task_submission.task_status": { $ne: "Submitted" }  
    }
  },
  {
    $project: {
      user_id: 1
    }
  }
]);


```



