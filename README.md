# CSE 11 Fall 2025 PA1 - Grade Calculator
**Due date: Thursday, October 9th @ 11:59PM PDT**

## Goal:
Programming Assignment 1 is an introduction to Java programming. In this PA, you will get exposure to primitive data types, variables, keyboard input, console output, if-else logic, and loops. 

## Grade Calculator [100 points]

Write a program called `GradeCalculator` that
- reads a student's scores for programming assignments (with resubmissions), quizzes (with makeups), attendance, midterm, and final exam
- calculates the overall score by applying CSE 11's actual grading scheme
- outputs the overall score and its corresponding letter grade

### Implementaion Details
Your file should be named as `GradeCalculator.java` and contains a single class `GradeCalculator` which has only 1 method: `main`.


There are a few restrictions on the implementation. 
- You should not import anything other than `java.util.Scanner`. If you import any other libraries, you may get a 0 for this part, even though if you had passed all the test cases.
- Please do not create multiple `Scanner` objects for `System.in`. They'll fight each other to get the input and cause all of your test cases to fail.
- You should not use `System.exit`. Doing so will cause Autograder to throw exceptions and you'll get "EXCEPT" for all of your test cases.
- You should not print any prompt (e.g. "please enter your score").
- **Do not declare instance variables or non-final static variables.** All your variables should be declared inside the `main` method (local variables). If you want to use constants, they must be declared as `private static final`.

To get started on this PA, Create a folder for "PA1" at the location of your choice and open that folder in Visual Studio Code (VS Code). Then create a class named `GradeCalculator` with a `main` method and save it as `GradeCalculator.java`.

#### Input

The program reads user inputs from `System.in` using a [Scanner](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/Scanner.html) (*remember to import it*). When a user runs `java GradeCalculator`, the input should be typed through the terminal and should contain exactly 5 lines. You can safely assume that all our inputs will follow this format.

**Scanner Usage Example:**
```java
import java.util.Scanner;  // At the top of your file

Scanner scanner = new Scanner(System.in);  // Create Scanner object
double num1 = scanner.nextDouble();        // Read a double
double num2 = scanner.nextDouble();        // Read another double
double result = num1 + num2;               // Use the values
scanner.close();                           // Close when done
```

**Line 1:** 18 space-separated numbers representing PA scores in pairs (original followed by resubmission)
- Format: `pa0 resub0 pa1 resub1 pa2 resub2 pa3 resub3 pa4 resub4 pa5 resub5 pa6 resub6 pa7 resub7 pa8 resub8`
- PA0 max score is 10 points, PA1-PA8 max score is 100 points each
- Resubmission scores can also be 0-10 for PA0 and 0-100 for PA1-PA8
- **Resubmission Policy:** Students can resubmit their work and receive up to 50% of their lost points. The final PA score is the maximum of: (1) the original score, or (2) the average of the original and resubmission scores

**Line 2:** 6 space-separated numbers representing quiz scores in pairs (original followed by makeup)
- Format: `quiz1 makeup1 quiz2 makeup2 quiz3 makeup3`
- Each quiz is out of 100 points (percentages)
- **Makeup Policy:** Each quiz has a corresponding makeup quiz. The final quiz score is the higher of the original quiz score or the makeup quiz score

**Line 3:** 1 integer representing attendance points earned (**must be an integer, not a decimal**)
- Range: 0-16 points (total lectures: 9 weeks × 2 lectures/week, minus midterm day and Thanksgiving)
- For grading, attendance is capped at a maximum of 10 points (we drop the lowest 6 lecture attendances)
- **Note:** All other scores (PAs, quizzes, exams) may be decimal values, but attendance must be a whole number (integer)

**Line 4:** 1 number representing midterm score (as a percentage)
- Range: 0-100

**Line 5:** 2 space-separated numbers representing final exam scores
- Format: `final_part1 final_total`
- `final_part1`: Score for part 1 of final exam (0-100, treated as percentage)
- `final_total`: Total final exam score (0-100, treated as percentage)
- If final part 1 percentage > midterm percentage, it replaces the midterm score

**Input Validation:**
- **IMPORTANT:** You must validate each input value **immediately** as you read it, before reading any subsequent values
- If any PA score (original or resubmission) is less than 0 or greater than the maximum for that PA (10 for PA0, 100 for PA1-PA8), print `invalid input` and **immediately return**
- If any quiz score (original or makeup) is less than 0 or greater than 100, print `invalid input` and **immediately return**
- If attendance is less than 0 or greater than 16, **or if it is not an integer** (e.g., 10.5), print `invalid input` and **immediately return**
- If midterm score is less than 0 or greater than 100, print `invalid input` and **immediately return**
- If either final exam score (part 1 or total) is less than 0 or greater than 100, print `invalid input` and **immediately return**

**Critical:** As soon as any invalid score is encountered, the program must print `invalid input` and terminate **without reading any more input values**.

#### Calculation

Follow these steps to calculate the final grade:

1. **Calculate Final PA Scores (with resubmission):**
   - For each PA (PA0-PA8): `final_pa_score = MAX((original + resubmission) / 2, original)`
   - This gives students up to 50% of lost points back

2. **Calculate PA Percentage:**
   - PA0 is worth 10 points, PA1-PA8 are each worth 100 points
   - Total PA points = PA0_final + PA1_final + PA2_final + ... + PA8_final
   - Maximum possible PA points = 10 + 100 + 100 + ... + 100 = 810 points
   - PA percentage = `(total PA points / 810) * 100`
   - Note: PA0 counts for less since it's worth fewer points

3. **Calculate Final Quiz Scores (with makeup):**
   - For each quiz (1-3): `final_quiz_score = MAX(original, makeup)`
   - Quizzes are already percentages (0-100), so just average them
   - Quiz percentage = `(sum of final quiz scores) / 3`

4. **Calculate Attendance Percentage:**
   - Capped attendance = `MIN(attendance, 10)`
   - Attendance percentage = `(capped_attendance / 10) * 100`

5. **Calculate Midterm Score (considering final part 1 replacement):**
   - Midterm score used = `MAX(midterm, final_part1)`
   - Both are treated as percentages (0-100)

6. **Calculate Overall Score using the Grade Breakdown:**

    | Component               | Weight |
    | ----------------------- | ------ |
    | Class Participation     | 10%    |
    | Programming Assignments | 40%    |
    | Quizzes                 | 10%    |
    | Midterm                 | 15%    |
    | Final Exam              | 25%    |

    Overall Score = `(attendance_pct * 0.10) + (pa_pct * 0.40) + (quiz_pct * 0.10) + (midterm_score * 0.15) + (final_total * 0.25)`
    
    Note: All components are percentages (0-100):
    - `attendance_pct` and `pa_pct` are calculated as percentages
    - `quiz_pct`, `midterm_score`, and `final_total` are already percentages from input

7. **Determine Letter Grade:**
    - A: overall score >= 90
    - B: 80 <= overall score < 90
    - C: 70 <= overall score < 80
    - D: 60 <= overall score < 70
    - F: overall score < 60

#### Output

Write the output to the standard output, which prints the overall score (formatted to **exactly 2 decimal places**) in the first line and the letter grade in the second line. There should be a **newline character** at the end of line. 
**Note:** `System.out.println()` method prints the text on the console and the cursor remains at the start of the next line at the console (i.e. it appends a newline character at the end of the line, so you **do not** need to add a newline character by yourself if you are using `System.out.println`). 
**Please follow the exact output format, or otherwise you will not get any credit.**


### Example Input and Output

#### Example 1 - Perfect Student

- Input
    ```
    > java GradeCalculator
    10 0 100 0 100 0 100 0 100 0 100 0 100 0 100 0 100 0
    100 100 100 100 100 100
    10
    100
    100 100
    ```
    - Line 1: PA scores in pairs (pa0 resub0 pa1 resub1 ...)
      - PA0=10, resub=0; PA1=100, resub=0; ... PA8=100, resub=0 (all perfect, no resubmissions needed)
    - Line 2: Quiz scores in pairs (quiz1 makeup1 quiz2 makeup2 quiz3 makeup3)
      - All quizzes=100, all makeups=100
    - Line 3: Attendance=10 (maximum that counts toward grade)
    - Line 4: Midterm=100
    - Line 5: Final part1=100, final total=100
    
- Output
    ```
    100.00
    A
    ```
    - PA percentage: 100%, Quiz percentage: 100%, Attendance: 100%, Midterm: 100, Final: 100
    - Overall: 100 * 0.1 + 100 * 0.4 + 100 * 0.1 + 100 * 0.15 + 100 * 0.25 = 100.0

#### Example 2 - Student with Resubmissions

- Input
    ```
    > java GradeCalculator
    8 2 80 90 60 80 70 70 90 95 85 90 75 85 80 90 95 100
    85 90 90 95 80 85
    10
    75
    88 92
    ```
    - Line 1: PA scores in pairs (pa resub pa resub ...)
      - PA0: 8, resub=2 → (8+2)/2 = 5 < 8, so final = 8
      - PA1: 80, resub=90 → (80+90)/2 = 85 > 80, so final = 85
      - PA2: 60, resub=80 → (60+80)/2 = 70 > 60, so final = 70
      - PA3: 70, resub=70 → (70+70)/2 = 70 = 70, so final = 70
      - PA4: 90, resub=95 → (90+95)/2 = 92.5 > 90, so final = 92.5
      - PA5: 85, resub=90 → (85+90)/2 = 87.5 > 85, so final = 87.5
      - PA6: 75, resub=85 → (75+85)/2 = 80 > 75, so final = 80
      - PA7: 80, resub=90 → (80+90)/2 = 85 > 80, so final = 85
      - PA8: 95, resub=100 → (95+100)/2 = 97.5 > 95, so final = 97.5
    - Line 2: Quiz scores in pairs (quiz makeup quiz makeup quiz makeup)
      - Quiz1: 85, makeup=90 → use 90
      - Quiz2: 90, makeup=95 → use 95
      - Quiz3: 80, makeup=85 → use 85
    - Line 3: Attendance=10
    - Line 4: Midterm=75
    - Line 5: Final part1=88, final total=92
    
- Output
    ```
    88.56
    B
    ```
    - PA: PA0 = 8, PA1-8 = 85 + 70 + 70 + 92.5 + 87.5 + 80 + 85 + 97.5 = 667.5
    - Total PA points = 8 + 667.5 = 675.5, PA percentage = 675.5/810 * 100 = 83.39506...%
    - Quiz: (90 + 95 + 85) / 3 = 90% (quizzes are already percentages)
    - Attendance: 10/10 * 100 = 100%
    - Midterm: 88 (replaced by final part 1 since 88 > 75)
    - Final: 92 (final total percentage)
    - Overall: 100 * 0.1 + 83.39506 * 0.4 + 90 * 0.1 + 88 * 0.15 + 92 * 0.25 ≈ 88.56

#### Example 3 - Invalid Input (PA score too high)

- Input
    ```
    > java GradeCalculator
    10 0 110 0 100 0 100 0 100 0 100 0 100 0 100 0 100 0
    INVALID_DATA_SHOULD_NOT_BE_READ
    INVALID_DATA_SHOULD_NOT_BE_READ
    INVALID_DATA_SHOULD_NOT_BE_READ
    INVALID_DATA_SHOULD_NOT_BE_READ
    ```

- Output
    ```
    invalid input
    ```
    *Note: PA1 score is 110 which exceeds 100. The program must **immediately** validate this and output "invalid input" after reading the first line, **without attempting to read lines 2-5**. If you try to read the subsequent lines (which contain non-numeric text), your program will crash!*

#### Example 4 - Invalid Input (Attendance too high)

- Input
    ```
    > java GradeCalculator
    10 0 100 0 100 0 100 0 100 0 100 0 100 0 100 0 100 0
    100 100 100 100 100 100
    20
    INVALID_DATA_SHOULD_NOT_BE_READ
    INVALID_DATA_SHOULD_NOT_BE_READ
    ```

- Output
    ```
    invalid input
    ```
    *Note: Attendance is 20 which exceeds the maximum of 16. After successfully reading lines 1-2, the program reads the attendance value on line 3, detects it's invalid, and must **immediately** output "invalid input" and return **without reading lines 4-5**.*

### Testing

#### Testing with Keyboard Input
You can test your program by typing input manually in the terminal:
```bash
java GradeCalculator
# Then type your inputs line by line
```

#### Testing with File Redirection (Recommended)
Instead of typing inputs manually every time, you can use **file redirection** to read input from a file. This is much more efficient for testing!

**How to use file redirection:**
1. Create a text file (e.g., `test.in`) with your test inputs
2. Run your program with the `<` operator:
   ```bash
   java GradeCalculator < test.in
   ```

**Example test files** are provided in the `starter/` directory:
- `test_perfect.in` - Perfect student (should output `100.00 A`)
- `test_resubmissions.in` - Student with resubmissions (should output `88.56 B`)
- `test_valid_with_decimals.in` - Valid test with decimal scores for PAs, quizzes, and exams (should output `91.74 A`)
- `test_invalid_pa.in` - Invalid PA score exceeding 100 (should output `invalid input`)
- `test_invalid_pa0.in` - Invalid PA0 score exceeding 10 (should output `invalid input`)
- `test_invalid_attendance.in` - Invalid attendance exceeding 16 (should output `invalid input`)
- `test_invalid_attendance_decimal.in` - Invalid decimal attendance like 10.5 (should output `invalid input`)

**Note:** The invalid test files contain non-numeric data on lines that should NOT be read. If your program tries to read past the invalid input, it will crash with an error. This confirms you're validating input immediately as required!

Try the example inputs described above. Do you get the same results as their corresponding outputs? Now try some of your own inputs, do you get the results you would expect?

## Submission
Submit all of the following files to Gradescope by **Thursday, October 9th @ 11:59PM PDT**.
 - `GradeCalculator.java`


You can submit as many times as you want on Gradescope, and you will see the results right after each submission. **Only for PA0 and PA1**, you are able to see all the test cases and results when you submit your assignment to Gradescope. It is your responsibility to test your program comprehensively. 

**Important:** Even if your code does not pass all the tests, you will still be able to submit your homework to receive partial points for the tests that you passed. **Make sure your code compiles on Autograder in order to receive partial credit. The name of the file needs to be `GradeCalculator.java`, otherwise you will receive no points.** 


### How your assignment will be evaluated [100 points]


* **Correctness** [100 points] You will earn points based on the autograder tests that your code passes. If the autograder tests are not able to run (e.g., your code does not compile or it does not match the specifications in this writeup), you may not earn any credit.
