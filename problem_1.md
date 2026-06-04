# Day 4 Problem 1

## Q1. Second Largest Without Duplicates

### Answer

Given an integer array, find and return the second largest unique
element. If no such element exists, return -1.

### C++ Solution

``` cpp
#include <iostream>
#include <vector>
#include <climits>
using namespace std;

int secondLargest(vector<int>& nums) {
    int largest = INT_MIN;
    int second = INT_MIN;

    for (int x : nums) {
        if (x > largest) {
            second = largest;
            largest = x;
        }
        else if (x < largest && x > second) {
            second = x;
        }
    }

    return second == INT_MIN ? -1 : second;
}
```

## Q2. Rotate Array K Steps to the Right

### Answer

Use reversal algorithm.

1.  k %= n
2.  Reverse entire array
3.  Reverse first k elements
4.  Reverse remaining elements

**Time Complexity:** O(n)

**Space Complexity:** O(1)

------------------------------------------------------------------------

## Q3. Valid Anagram Check

### Answer

Convert both strings to lowercase, ignore spaces, and compare character
frequencies using a frequency array/hash map.

**Time Complexity:** O(n)

------------------------------------------------------------------------

## Q4. Longest Substring Without Repeating Characters

### Answer

Use Sliding Window with two pointers and a map storing the last seen
index of each character.

**Time Complexity:** O(n)

------------------------------------------------------------------------

## Q5. Top Earning Employees per Department

### Answer

``` sql
SELECT d.dept_name, e.name, e.salary
FROM Employees e
JOIN Departments d ON e.dept_id = d.dept_id
JOIN (
    SELECT dept_id, MAX(salary) AS max_salary
    FROM Employees
    GROUP BY dept_id
) m
ON e.dept_id = m.dept_id
AND e.salary = m.max_salary;
```

------------------------------------------------------------------------

## Q6. Monthly Order Revenue Report

### Answer

Use `SUM(amount)`, `COUNT(*)`, `GROUP BY month`, and
`ORDER BY total_revenue DESC` for 2024 orders.

------------------------------------------------------------------------

## Q7. Fetch and Display User Posts

### Answer

Send GET request to:

`https://jsonplaceholder.typicode.com/posts?userId=3`

Convert titles to uppercase, print them, and print total count.

Handle HTTP errors using try-catch.

``` javascript
async function getPosts() {
    try {
        const response = await fetch(
            "https://jsonplaceholder.typicode.com/posts?userId=3"
        );

        if (!response.ok) {
            throw new Error(`HTTP Error ${response.status}`);
        }

        const posts = await response.json();

        posts.forEach(post => {
            console.log(post.title.toUpperCase());
        });

        console.log(`Total posts: ${posts.length}`);
    }
    catch (err) {
        console.error("Request failed:", err.message);
    }
}

getPosts();
```

------------------------------------------------------------------------

## Q8. Weather Summary with Conditional Logic

### Answer

Call Open-Meteo API, read `current_weather.temperature` and
`current_weather.weathercode`, then map weather codes to readable
conditions using a dictionary/object.

``` javascript
async function weather() {
    try {
        const lat = 51.5072;
        const lon = -0.1276;

        const url =
            `https://api.open-meteo.com/v1/forecast?latitude=${lat}&longitude=${lon}&current_weather=true`;

        const response = await fetch(url);

        if (!response.ok)
            throw new Error("API request failed");

        const data = await response.json();

        const codeMap = {
            0: "Clear sky",
            1: "Mainly clear",
            2: "Partly cloudy",
            3: "Overcast",
            45: "Fog",
            61: "Light rain",
            80: "Rain showers",
            95: "Thunderstorm"
        };

        console.log("City       : London");
        console.log(
            `Temperature: ${data.current_weather.temperature}°C`
        );
        console.log(
            `Condition  : ${codeMap[data.current_weather.weathercode] || "Unknown"}`
        );
    }
    catch (err) {
        console.error(err.message);
    }
}

weather();
```
