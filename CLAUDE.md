# Medical Staff Scheduler - Project Documentation

## Overview
A web application for scheduling medical staff (doctors) with automated weekday scheduling and manual weekend/vacation/holiday entry.

## Doctors
- **Auto-assigned (5):** Margolies, Racine, Mckeown, Jackson, Piennette
- **Manual only:** Fellow (never auto-assigned, only entered manually when needed)

## Assignment Types
| Assignment | Description |
|------------|-------------|
| call | On-call duty |
| mw2 | Midweek assignment 2 |
| mw3 | Midweek assignment 3 |
| post | Post-call (day after being on call) |
| off | Day off |
| vacation | Vacation day (manually entered) |

## Schedule Structure

### Weekdays (Monday - Friday)
Each day must have all assignments filled:
- call
- mw2
- mw3
- post
- off OR vacation (one or the other)

**All 5 auto-assigned doctors must have exactly one assignment per weekday.**

### Weekends (Saturday, Sunday)
- Weekend call covers Friday, Saturday, and Sunday
- Assigned manually via the "Weekend Call" section
- Only the "call" assignment is shown for Saturday and Sunday
- Friday shows full assignments (other doctors still need mw2, mw3, post, off)

### Holidays
Holidays are call-only days (like weekends) and must be assigned manually:
| Holiday | Date |
|---------|------|
| New Year's Day | January 1 |
| 4th of July | July 4 |
| Memorial Day | Last Monday of May |
| Labor Day | First Monday of September |
| Thanksgiving | 4th Thursday of November |
| Christmas Day | December 25 |

## UI Controls

### Top Button Bar
| Button | Description |
|--------|-------------|
| **Auto-Fill Weekdays** | Runs the scheduling algorithm to fill Mon-Fri assignments |
| **Check for Errors** | Validates schedule against all rules, reports errors and warnings |
| **Report** | Shows fairness report with weekday call and off counts per doctor |
| **Print** | Opens print dialog for the calendar |
| **Download** | Downloads calendar as an HTML file |
| **Clear All** | Clears all assignments for the current month |
| **Save** | Saves schedule to a JSON file |
| **Load** | Loads schedule from a JSON file |
| **Share** | Dropdown menu with sharing options (see below) |

### Share Options
| Option | Description |
|--------|-------------|
| Email (Text) | Opens email with schedule as plain text |
| Text Message | Opens SMS app with schedule text (mobile) |
| Copy Text | Copies plain text schedule to clipboard |
| Email (Calendar) | Opens calendar in new tab + email client |
| Open Calendar in Tab | Opens visual calendar in new browser tab |
| Copy Calendar HTML | Copies full HTML code to clipboard |

### Make Assignment Section
- Select assignment type, doctor, start date, and optional end date
- **Assign** - Creates the assignment(s)
- **Unassign** - Removes the assignment(s)

### Weekend Call Section
- Select doctor and weekend (shown as Friday date)
- Automatically assigns Friday, Saturday, Sunday call + Monday post
- **Assign** - Creates weekend assignment
- **Clear** - Removes weekend assignment

### Clear Days Section
- Select start date and optional end date
- **Clear Days** - Removes ALL assignments for selected date range
- Useful for making manual adjustments without clearing entire month

## Auto-Fill Rules

### Core Rule
- **One assignment per doctor per day**: Each doctor can only have ONE assignment each day (call, mw2, mw3, post, off, or vacation)

### Call Assignment (Monday - Thursday)
1. Equal distribution of weekday calls among the 5 doctors
2. Rotate call days so each doctor gets equal Mondays, Tuesdays, Wednesdays, Thursdays
3. **No consecutive calls** - a doctor cannot be on call two days in a row
4. **Max 1 weekday call per week** - no doctor should have more than one Mon-Thu call assignment per week
5. Friday call is part of weekend (assigned manually)
6. **Pre-weekend rule**: In weeks leading up to a doctor's weekend call, that doctor should only be assigned weekday call on Monday or Tuesday if possible (not Wednesday or Thursday)

### Post Assignment
- Always given to the doctor who was on call the previous day
- Will not be assigned if that doctor already has another assignment (e.g., vacation)

### Off Assignment Rules
1. **One off per week per doctor**: Each doctor should receive exactly one off assignment per week (when no vacations)
2. **Day before call preference**: Off should preferably be assigned the day before the doctor's call day
3. **Equal monthly distribution**: Total off assignments per month should be as equal as possible among all doctors
4. **No off when vacation**: When someone has vacation, there is no off assignment that day
5. If tomorrow's call doctor already has an off this week, assign off to another doctor who still needs one (sorted by fewest monthly offs)

### MW3 Assignment Rules
1. **When vacation exists**: mw3 goes to the doctor on call the following day
2. **When tomorrow's call doctor can't get off**: If the doctor on call tomorrow already has an off this week (and there's no vacation), they get mw3 instead
3. **Otherwise**: mw3 is assigned to remaining available doctors

### MW2 Assignment
- Assigned to remaining available doctors after call, post, and off/mw3 rules are applied

### Cleanup Step
After all assignment logic, the system ensures:
- All 5 doctors have exactly one assignment each weekday
- Any empty slots are filled with remaining unassigned doctors

### Special Rules

#### Margolies 3rd Wednesday Rule
On the **3rd Wednesday of each month**, Margolies must be assigned **call, mw2, or mw3** only.
- Margolies **cannot** be assigned **off** or **post** on the 3rd Wednesday
- Exception: If Margolies has vacation (manually entered), this rule does not apply
- The auto-fill algorithm automatically enforces this rule

## Check for Errors

The error checker validates:

### Errors (Must Fix)
- Missing assignments (all slots must be filled)
- Double assignments (doctor has more than one assignment per day)
- Unassigned doctors (all 5 must have an assignment each weekday)
- Consecutive calls (same doctor on call two days in a row)
- Weekend call mismatch (Fri/Sat/Sun should have same doctor)
- Missing weekend/holiday call
- Margolies assigned off or post on 3rd Wednesday (unless on vacation)

### Warnings (Review)
- Post doesn't match yesterday's call doctor
- Monday post doesn't match Sunday call doctor
- Multiple weekday calls per week for same doctor

## Report

The fairness report shows:
- **Weekday Calls** - Number of Mon-Thu call assignments per doctor
- **Off Days** - Number of off assignments per doctor

This helps ensure equitable distribution across doctors.

## Workflow
1. Select month and year (calendar updates automatically)
2. Manually enter holiday call assignments
3. Manually enter weekend call assignments (Fri/Sat/Sun)
4. Manually enter any vacation days
5. Click "Auto-Fill Weekdays" to generate the rest of the schedule
6. Click "Check for Errors" to validate
7. Click "Report" to verify fair distribution
8. Make manual adjustments as needed (use "Clear Days" to clear specific days)
9. Save/Print/Download/Share the schedule

## File Structure
- `index.html` - Single-file application containing HTML, CSS, and JavaScript
- `CLAUDE.md` - This documentation file
