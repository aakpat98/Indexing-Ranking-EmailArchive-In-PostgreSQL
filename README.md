# Email Archive Indexing and Ranking with PostgreSQL and Python

This project demonstrates how to utilize PostgreSQL's full-text search capabilities to index and rank email archives using Python. By leveraging the `TSVector` data type, the project enables efficient natural language searching within email bodies.

## Overview

The goal of this project is to fetch email data, store it in a PostgreSQL database, and create a full-text search index to facilitate efficient querying. The process involves:

1. **Data Retrieval**: Using Python to pull email data from the web.
2. **Database Storage**: Storing the retrieved emails in a PostgreSQL table.
3. **Indexing**: Creating a natural language index on the email body using PostgreSQL's `TSVector`.
4. **Querying**: Performing full-text searches to retrieve and rank emails based on relevance.

## Running the Script

To fetch email data and insert it into your PostgreSQL database, execute:

```bash
python gmane.py
```

This script will:

- Fetch email data from the web.
- Process and insert the data into the `messages` table in PostgreSQL.

## Database Schema

The `messages` table is structured as follows:

```sql
CREATE TABLE IF NOT EXISTS messages (
    id SERIAL PRIMARY KEY,
    email TEXT,
    sent_at TIMESTAMPTZ,
    subject TEXT,
    headers TEXT,
    body TEXT
);
```

## Indexing

To enable efficient full-text search, create a `TSVector` index on the `body` column:

```sql
CREATE INDEX messages_gin ON messages USING gin(to_tsvector('english', body));
```

This index allows for fast text search queries using PostgreSQL's full-text search functions.

## Querying

With the index in place, you can perform searches to find emails containing specific terms:

```sql
SELECT subject, sender
FROM messages
WHERE to_tsquery('english', 'search_term') @@ to_tsvector('english', body)
LIMIT 10;
```

Replace `'search_term'` with your desired search keyword.

## Ranking Results

To rank search results by relevance, use the `ts_rank` function:

```sql
SELECT id, subject, sender,
       ts_rank(to_tsvector('english', body), to_tsquery('english', 'search_term')) AS rank
FROM messages
WHERE to_tsquery('english', 'search_term') @@ to_tsvector('english', body)
ORDER BY rank DESC
LIMIT 10;
```

This query returns the top 10 most relevant emails containing the search term.

## Resources

- [PostgreSQL Full-Text Search Documentation](https://www.postgresql.org/docs/current/textsearch.html)
- [Python's `psycopg2` Library](https://www.psycopg.org/docs/)

---

By following this guide, you can successfully index and rank email archives using PostgreSQL's full-text search capabilities, enabling efficient search and retrieval of email data. 
