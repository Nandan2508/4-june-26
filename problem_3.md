```python
md_content = """# Employee Attendance Tracker: Bug Fix Report

Stepping into a codebase to clean up a trail of bugs left behind is a classic rite of passage for any developer! It can be a bit frustrating to untangle someone else's logic, but it's also highly satisfying when everything finally compiles and runs seamlessly.

Let's knock out these bugs one module at a time. Here is the corrected code for each scenario, along with a brief explanation of the fixes.

---

## BUG 01 · `api_client.py`

**Fixes:**
1. Added the `headers` dictionary to pass the Bearer token for authorization.
2. Added a check for the `404` status code to return `None` gracefully instead of throwing a `KeyError`.


```

```text
File saved at employee_attendance_fixes.md

```python
import requests

API_BASE_URL = "[https://hr-internal.company.com/api](https://hr-internal.company.com/api)"
API_TOKEN = "your_secret_token_here" # Assuming this constant is defined

def get_employee(employee_id):
    url = f"{API_BASE_URL}/employees/{employee_id}"
    headers = {"Authorization": f"Bearer {API_TOKEN}"}
    
    # Bug A Fixed: Pass headers to the request
    response = requests.get(url, headers=headers)
    
    # Bug B Fixed: Gracefully handle 404 Not Found
    if response.status_code == 404:
        return None
        
    data = response.json()
    # Using .get() is also a safer way to avoid KeyErrors
    return data.get("employee") 

```

---

## BUG 02 · `db_queries.py`

**Fixes:**

1. Changed `LEFT JOIN` to `INNER JOIN` so the query strictly requires a valid match in the `employees` table.
2. Added `a.employee_id = ?` to the `WHERE` clause and updated the tuple passed into `cursor.execute()` to properly filter the data.

```python
def get_monthly_attendance(conn, employee_id, month, year):
    query = \"\"\"
        SELECT a.date, a.check_in, a.check_out, e.name
        FROM attendance a
        INNER JOIN employees e                  -- Bug A Fixed: Changed to INNER JOIN
        ON a.employee_id = e.id
        WHERE a.month = ?                       
        AND a.year = ?
        AND a.employee_id = ?                   -- Bug B Fixed: Added employee_id filter
        ORDER BY a.date ASC
    \"\"\"
    cursor = conn.cursor()
    # Passed employee_id into the execution tuple
    cursor.execute(query, (month, year, employee_id))
    return cursor.fetchall()

```

---

## BUG 03 · `attendance.py`

**Fixes:**

1. Swapped the subtraction order to `co - ci` (check-out minus check-in) so the duration is positive.
2. Updated the comparison operator to `>=` to ensure hitting exactly 15 minutes triggers the late flag.

```python
from datetime import datetime

WORK_START = "09:00"
LATE_THRESHOLD_MINUTES = 15

def calculate_hours(check_in: str, check_out: str):
    fmt = "%H:%M"
    ci = datetime.strptime(check_in, fmt)
    co = datetime.strptime(check_out, fmt)

    duration = co - ci                                    # Bug A Fixed: Correct subtraction order
    hours_worked = duration.seconds / 3600

    start = datetime.strptime(WORK_START, fmt)
    late_by = (ci - start).seconds // 60

    is_late = late_by >= LATE_THRESHOLD_MINUTES           # Bug B Fixed: Changed > to >=

    return round(hours_worked, 2), is_late

```

---

## BUG 04 · `report.py`

**Fixes:**

1. Replaced the error-prone `range()` loop with a Pythonic `for record in records:` loop, eliminating the out-of-bounds `IndexError`.
2. Added a conditional check for `avg_hours` to handle scenarios where `records` is empty, preventing the `ZeroDivisionError`.

```python
def generate_summary(records):
    total_hours = 0
    late_days = 0

    # Bug A Fixed: Iterate directly over items (prevents IndexError)
    for record in records:
        total_hours += record["hours"]
        if record["is_late"]:
            late_days += 1

    # Bug B Fixed: Protect against ZeroDivisionError
    avg_hours = (total_hours / len(records)) if len(records) > 0 else 0

    return {
        "total_hours": round(total_hours, 2),
        "avg_hours": round(avg_hours, 2),
        "late_days": late_days,
        "days_present": len(records)
    }

```

---

## BUG 05 · `report.py`

**Fixes:**

1. Flipped the `below_minimum` check to `<` so it flags employees failing to meet the 20-day minimum.
2. Updated the `exceeded_late` check to `>=` because 3 or more late check-ins trigger the warning based on the updated HR policy rule.

```python
MIN_DAYS_REQUIRED = 20
MAX_LATE_DAYS = 3

def check_attendance_policy(summary):
    days_present = summary["days_present"]
    late_days = summary["late_days"]

    below_minimum = days_present < MIN_DAYS_REQUIRED     # Bug A Fixed: Flipped to <
    exceeded_late = late_days >= MAX_LATE_DAYS           # Bug B Fixed: Changed to >=

    if below_minimum or exceeded_late:
        return {
            "warning": True,
            "reason": []
              + (["Below minimum attendance"] if below_minimum else [])
              + (["Exceeded late check-ins"] if exceeded_late else [])
        }
    return {"warning": False, "reason": []}

```

"""

file_path = "employee_attendance_fixes.md"
with open(file_path, "w", encoding="utf-8") as file:
file.write(md_content)

print(f"File saved at {file_path}")

```
