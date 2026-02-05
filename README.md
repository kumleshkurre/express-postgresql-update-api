# Express PostgreSQL Update API 🚀

A simple **Express.js REST API** that demonstrates how to **update records in PostgreSQL** using PUT requests.  
This project includes **request body parsing**, **input validation**, and **safe database operations**.

---
## 📂 Project Structure
```
express-postgresql-insert-api
│
├── db.js # PostgreSQL connection pool
├── update.js # Express server & API routes
├── package.json
└── README.md
```
## ⚙️ Configure the PostgreSQL connection pool (`db.js`)
```
const { Pool } = require('pg');
const pool = new Pool({
   user: 'your_username',
   host: 'localhost',
   database: 'your_database_name',
   password: 'YOUR_PASSWORD',
   port: 5432,                     // Default PostgreSQL port

  });
  
  module.exports = pool;
```
## Create Your Express Server
Create a file: update.js
```
// update.js
const express = require('express');
const { body, validationResult } = require('express-validator');    //insert validation
const pool = require('./db');
const bodyParser = require('body-parser');
const app = express();
const PORT = 4000;

// Middleware to parse JSON
app.use(bodyParser.json());

app.get('/',(req, res) => {
    res.send(`<h1>EXPRESS JS API</h1>`);
});
//-----------------------------------bodyparser--------------------------------------------------
// Define a route to query the database
app.put('/upemploye', async (req, res) => {
    try {
      const { mobile,name,id } = req.body; // Get 'menu_name,menu_price,gid,qid' from route parameters
      const result = await pool.query('update employe set mobile=$1,name=$2 where id=$3',[mobile,name,id]);
      res.json(result.rows);
    } catch (err) {
      console.error('Error executing query', err.stack);
      res.status(500).send('Internal Server Error');
    }
  });
  //------------------------------with validation------------------------------------------------
// Define a route to query the database
app.put('/upemployeById', [ 
  body('mobile').notEmpty().withMessage('mobile is required'),
  body('name').notEmpty().withMessage('name is required'),
  body('id').notEmpty().withMessage('id is required')
]   ,async (req, res) => {
  try {
      const errors = validationResult(req);
   if (!errors.isEmpty()) {
  return res.status(400).json({ errors: errors.array() });
   }else{
  const { mobile,name,id } = req.body; 
   const rs= await pool.query('Select * FROM employe WHERE id = $1', [id]);
  if (rs.rows.length > 0) {
        await pool.query('update employe set mobile=$1,name=$2 where id=$3',[mobile,name,id]);
         res.status(200).json({ status: 'success', message: 'update successful' });
      } else {
        res.status(400).json({ status: 'failed', message: 'update failed' });
      }
  }
  }catch (err) {
    console.error('Error executing query', err.stack);
    res.status(500).send('Internal Server Error');
  }
});
  // Start the server
  app.listen(PORT,() => {
    console.log(`Server running on http://localhost:${PORT}`);
  });  
```
## 🚀 API Endpoints
### 🔹 Test API
GET /
#### Response:
```
<h1>EXPRESS JS API</h1>
```
### 🔹 Update Employee (Without Validation)
PUT /upemploye
#### Request Body:
```
{
  "id": 1,
  "mobile": "9876543210",
  "name": "Rohit"
}
```
### 🔹 Update Employee (With Validation)
PUT /upemployeById
#### Request Body:
```
{
  "id": 1,
  "mobile": "9999999999",
  "name": "Amit"
}
```
- Validation Rules
- id is required
- mobile is required
- name is required
```
Success Response
{
  "status": "success",
  "message": "update successful"
}

Error Response (ID Not Found)
{
  "status": "failed",
  "message": "update failed"
}
{
  "errors": [
    {
      "type": "field",
      "msg": "name is required",
      "path": "name",
      "location": "body"
    }
  ]
} 

## 👨‍💻 Author

- Kumlesh Kurre
- Backend Developer
- Skills: Express.js | Node.js | PostgreSQL | REST APIs
 
## ⭐ Support
If you like this project, please ⭐ star the repository to support my work!
    
