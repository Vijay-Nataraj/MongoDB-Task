# Zen Class Program Database

This project is a MongoDB database design for a Zen class program that includes various collections to manage users, tasks, attendance, topics, company drives, and mentors. The database is designed to facilitate the management of class activities, track attendance, and monitor task submissions.

## Features

- Manage user information and track their attendance.
- Create and manage tasks with submission status.
- Track topics taught in the class.
- Manage company drives and participants.
- Assign mentors to users and track mentee counts.

## Setup Instructions

#### 1. Clone the Repository

Clone the repository to your local machine:

```bash

      git clone https://github.com/Vijay-Nataraj/MongoDB-Task.git

```

#### 2. Navigate to the project directory:

```bash

      cd MongoDB-Task

```

#### 3. Install MongoDB and set up your database.

## MongoDB Queries:

#### 1. Find all the topics and tasks which are thought in the month of October

```json

   db.getCollection('topics').find(
   {
      date: {
         $gte : '2023-10-01',
         $lte: '2023-10-31'
      }
   }).toArray();

   db.getCollection('tasks').find(
   {
      due_date: {
         $gte : '2023-10-01',
         $lte: '2023-10-31'
      }
   }).toArray();

```

<!--

````json
db.getCollection('topics').aggregate([
  {
    // Convert the date string into a Date object
    $project: {
      dateAsDate: { $dateFromString: { dateString: "$date" } },
      topic_name: 1,
      _id: 1
    }
  },
  {
    // Extract the month from the date
    $project: {
      month: { $month: "$dateAsDate" },
      topic_name: 1
    }
  },
  {
    // Match only documents where the month is October (month 10)
    $match: {
      month: 10
    }
  }
]);

db.getCollection('tasks').aggregate([
  {
    // Convert the date string into a Date object
    $project: {
      dateAsDate: { $dateFromString: { dateString: "$due_date" } },
      task_name: 1,
      submitted: 1,
      _id: 1
    }
  },
  {
    // Extract the month from the date
    $project: {
      month: { $month: "$dateAsDate" },
      task_name: 1,
      submitted: 1
    }
  },
  {
    // Match only documents where the month is October (month 10)
    $match: {
      month: 10
    }
  }
]);
``` -->

#### 2. Find all the company drives which appeared between 15 oct-2020 and 31-oct-2020

```json

   db.getCollection('company_drives').find(
   {
      date: {
         $gte: '2020-10-15',
         $lte: '2020-10-31'
      }
   }).toArray();

```

#### 3. Find all the company drives and students who are appeared for the placement.

```json

   db.getCollection('company_drives').find(
   {},
   {
      company_name: 1,
      participants: 1
   }).toArray();

```

#### 4. Find the number of problems solved by the user in codekata

```json

   db.getCollection('codekata').aggregate(
   [
      {
         $group:{
            _id: 'Total Problem Solved By Users',
            count: { $sum: '$problems_solved' }
         }
      }
   ]);

```

#### 5. Find all the mentors with who has the mentee's count more than 15

```json

   db.getCollection('mentors').find(
      {
         mentees_count: { $gt: 15 }
      }
   );

```

#### 6. Find the number of users who are absent and task is not submitted between 15 oct-2020 and 31-oct-2020

```json

db.users.aggregate([
  {
    $unwind: "$attendance"
  },
  {
    $match: {
      "attendance.status": "absent",
      "attendance.date": {
        $gte: "2020-10-15",
        $lte: "2020-10-31"
      }
    }
  },
  {
    $lookup: {
      from: "tasks",
      let: { userId: "$_id" },
      pipeline: [
        {
          $match: {
            submitted: false,
            due_date: {
              $gte: "2020-10-15",
              $lte: "2020-10-31"
            }
          }
        },
        {
          $project: {
            _id: 1
          }
        }
      ],
      as: "unsubmitted_tasks"
    }
  },
  {
    $match: {
      "unsubmitted_tasks.0": { $exists: true }
    }
  },
  {
    $project: {
      _id: 0,
      name: 1
    }
  },
  {
    $count: "num_users"
  }
])

```
