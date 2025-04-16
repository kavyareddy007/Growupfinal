# JobSort - Resume-Based Job Ranking System

## Overview
This Python module ranks job listings for a candidate based on their resume data. It extracts resume details, compares them with job roles,skills,experience,location,salary etc.. stored in a MongoDB database, scores matches based on relevance, and returns the top-ranked job opportunities.

---
final/
├── sorting.py        #  Main script: loads resume, decodes it, ranks job matches
├── README.md         #  Project documentation

## Module Imports

### `heapq`
Used to manage a priority queue for job rankings. It allows quick retrieval of top-scoring job matches by maintaining a heap structure.

### `ResumeDecoder`
A custom class responsible for parsing and decoding resume text (likely from a PDF) into structured data like skills, roles, location, and experience.

### `MongoClient` (from `pymongo`)
Allows connection to a MongoDB database to fetch job listings.

### `itertools`
Used for `count()`, which helps maintain a unique ordering of elements in the heap, even when multiple jobs have the same score.

---

## Class: `jobSort`

### Purpose
Encapsulates logic to decode resume data, retrieve job listings, compare and rank jobs based on relevance.

---

## Constructor: `__init__(self, pdf_text)`

1. **Resume Decoding**
   - An instance of ResumeDecoder is created using the resume text (usually from a PDF).
   - The response() method gives back two things:
        - A dictionary with structured details like skills, experience, city, and more.
        - A list of predicted job roles based on the resume content.

2. **Role Integration**
   - Adds the extracted `role_name` list into the resume dictionary under the key `roles`.

3. **MongoDB Connection**
   - Connects to a MongoDB database using the provided connection string.
   - Accesses the `Growup` database and the `jobs` collection.
   - Retrieves all job entries and stores them in `self.company_roles`.

4. **Data Storage**
   - Stores resume data in `self.resume_data` for use in the ranking function.

---

## Method: `normalize(self, items)`

### Purpose
Makes sure text is clean and consistent before comparing.
   - If it’s a list, it turns each item into a lowercase, trimmed version (and removes duplicates).
   - If it’s a single string, it just trims spaces and makes it lowercase.
   - This way, "Python", " python ", and "PYTHON" all become the same for matching.
---

## Method: `rank_companies(self)`

### Purpose
Matches resume data with job listings and ranks them by relevance.

**Extract Resume Info**
   - Extracts roles, skills, experience, and city from the parsed resume.
   - Normalizes roles, skills, and city for accurate comparison.

**Initialize Heap & Counter**
   - A heap (`heapq`) is used to store job matches with negative scores (to simulate max-heap behavior).
   - A counter ensures uniqueness of heap entries with equal scores.

**Job Matching Loop**
   - Iterates through each job listing.
   - Normalizes job role, skills, and location.
   - Extracts job experience range (from and to).

**Scoring Logic**
   - Calculates intersections between resume and job roles/skills.
   - Assigns a score:
     - +1 for each matched skill
     - +1 for each matched role
     - +1 if location matches
     - +1 if candidate’s experience >= job’s minimum requirement

**Heap Push**
   - If a job has a score > 0, it's considered a good match.
   - Job is pushed into the heap with negative score (to simulate max priority).

**Top Job Extraction**
   - Pops up to 6 highest-scoring jobs from the heap.
   - For each job, collects:
     - Company name
     - Role
     - Matched skills
     - Whether location and experience matched
     - Total score
   - Appends result to a list called `ranked`.

**Return Value**
   - Returns a list of the top-ranked jobs based on resume-job relevance.

---




