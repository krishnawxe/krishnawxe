
const express = require('express');
const bodyParser = require('body-parser');
const { Pool } = require('pg');

const app = express();
const port = 5000;

// PostgreSQL configuration
const pool = new Pool({
  user: 'postgre',
  host: 'localhost',
  database: 'yt_newapp',
  password: 'tejas',
  port: 5432,
});

app.use(bodyParser.json());

// Endpoint to fetch paginated data
app.get('/adduser', async (req, res) => {
  try {
    const { page, search } = req.query;
    const offset = (page - 1) * 20;
    let query = 'SELECT * FROM krishna';

    if (search) {
      query += ` WHERE column_name ILIKE '%${search}%'`;
    }

    query += ` LIMIT 20 OFFSET ${offset}`;

    const client = await pool.connect();
    const result = await client.query(query);
    const totalRows = await client.query('SELECT COUNT(*) FROM krishna');
    const totalCount = parseInt(totalRows.rows[0].count);
    res.json({ data: result.rows, totalCount });
    client.release();
  } catch (error) {
    console.error('Error executing query', error);
    res.status(500).json({ error: 'Internal Server Error' });
  }
});

app.listen(port, () => {
  console.log(`Server is running on port ${port}`);
});
