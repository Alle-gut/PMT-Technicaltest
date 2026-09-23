# PMT-Technicaltest
Technical test for Dev
Language used PYTHON
# English Guide
###1.STEP BY STEP RUNNING:
RUN FOLLOWING COMMANDS FOR REQUIREMENTS/DEPENDENCY
pip install fastapi
pip install python-crontab
pip install sqlite3

FURTHER EXPLANATIONS REGARDING MY CHOICES:
1-SQLITE DB already setted up, populated as "PMT_subscriber.db". SQLite was used to interviewer can just download everything and run directly to check.
2-Production must've used PosgreSQL or other big non-portable DB for better handling of multiple writing request, I use SQLite for easy test
3-In the script, i use conn.close() for every request for simplicity n avoid opened resource. For handling multiple async request in production Connection -pooling will be used.
4-main.py API's were used as link for snapshots and general access to DB, easy and close enough to real usage

ARCHITECTURE:

<img width="717" height="427" alt="image" src="https://github.com/user-attachments/assets/57107a02-936f-467a-858a-b50b517b3f0c" />


###2.ANSWER TO QUESTIONS:
Q1:
Answer is in main.py, which is the API that handles POST&GET methods, will open endpoint at http://localhost:8888/.
Methods exposed: 
GET /subscribers (get a subscriber data)
POST /usage (input subscriber usage data)
GET /usage (get all usage data)
GET /usage/{subscriber_id} (get only that subscriber usage data)
Run main.py with uvicorn main:app --host 127.0.0.1 --port 8888, then test via 'testusage.py' (i use py requests for simplicity)
Do not shutdown main.py for next question as the snapshot functionality use the endpoints.

Q2: 

<img width="1613" height="120" alt="image" src="https://github.com/user-attachments/assets/7944d840-39e3-4a45-83c9-b142194d7ff8" />

Answer is in Cron scheduler folder which contains:
'snapshot.py' for snapshots
'cleanup.py' for 30 day cleanup
'crontimerset.py' for setting the timer to run snapshot.py every 08.00, 12.00, 15.00 LOCAL TIME, I expect the interviewer use WIB timezone. can be changed
'removecron.py' for remove all jobs with 'subscriber_usage' comment, basically any cron job related to the test script.
Run 'crontimerset.py' and wait, it will do automatic snapshots as scheduled. CSV which will be saved in snapshots folder as 'subscriber_usage_{date}_{time}.csv' and also run the 30 day cleanup script.
Run 'removecron.py' when done testing so your computer won't run the scripts automatically afterwards.

Q3:

<img width="540" height="295" alt="Screenshot from 2026-09-23 16-48-22" src="https://github.com/user-attachments/assets/39c85d52-b6c6-4375-9ed0-a29d6e352821" />

For testing yourself:
run DBreset.py to delete Fajar entry
Open DBrun.py which contains all the answers to the test.
conn.commit() last to execute all SQL commands done. You can delete Fajar using 'resetDB.py'

Q4:
Buggy Code

function getTotalUsageMB(records) {
  return records.reduce((total, record) => {
    total += record.dataUsageMB;
  });
}

Root Cause: Array.reduce() have no initial value
- Always pass an explicit initial value to `reduce()` when accumulating from an array of objects
- TypeScript type mismatch between accumulator and element would be caught at compile time

Fix
function getTotalUsageMB(records) {
  return records.reduce((total, record) => {
    total += record.dataUsageMB;
    return total;
  }, 0);
}

added return so total actually return value
