# User Management UI Specification

## 1. Overview

The **User Management** page allows administrators to view, filter, create, and update user records.

The interface is divided into two main sections:

- **User List Table** (left panel)
- **User Details Form** (right panel)

---

## 2. Page Behavior & Initial State

### 2.1 Initial Load
- The system must fetch the first page of users from the API.
- The retrieved users must be displayed in the User List Table.
- A loading spinner must be displayed while data is being fetched.

### 2.2 Default Selection
- By default, no user is selected.
- The User Details Form must be empty and inactive until:
  - A user is selected from the table, or
  - The "New User" button is clicked.

### 2.3 Responsiveness
- On desktop screens, the layout must be displayed in split-screen format.
- On smaller screens (tablet/mobile), the form may stack below the table.

---

## 3. UI Components

---

### 3.1 Top Control Bar

#### 3.1.1 New User Button
- **Action:** Resets the User Details Form to empty values.

#### 3.1.2 Hide Disabled User Checkbox
- **Behavior:**
  - When checked: Filter out users where `Enabled == false`.
  - When unchecked: Display all users.
- Filtering must happen instantly without page reload.

---

### 3.2 User List Table

The table displays user records with the following columns:

| Column Name | Data Type | Description |
|------------|-----------|-------------|
| ID | Integer | Unique identifier (generated sequentially or randomly) |
| User Name | String | Username of the user |
| Email | String | User email address |
| Enabled | Boolean | Displayed as `true` or `false` |

#### Table Features

- **Sorting**
  - All columns must support ascending and descending sorting.
  - Sorting is triggered via arrow icons in the header.

- **Filtering**
  - Each column includes a filter icon.
  - Users can search specific values per column.

- **Row Selection**
  - Clicking a row:
    - Highlights the row.
    - Loads the selected user's data into the User Details Form.
    - Switches the form to "Edit Mode".

---

### 3.3 User Details Form

This form supports both **Create** and **Edit** operations.

| Field | Component Type | Constraints |
|-------|---------------|------------|
| Username | Text Input | Required. Must be unique. |
| Display Name | Text Input | Required. |
| Phone | Text Input | Optional. Must match numeric/phone format. |
| Email | Text Input | Required. Must follow `example@domain.com` format. |
| User Roles | Multi-select Dropdown | Options: Guest, Admin, SuperAdmin |
| Enabled | Checkbox | Default: Unchecked (`false`) |

---

## 4. Interaction Logic

---

### 4.1 Save User Action & Validation

When the **"Save User"** button is clicked:

#### Step 1: Form Validation
- Verify all required fields are filled.
- Validate email format using regex.
- If validation fails:
  - Display descriptive error messages in red below the relevant input fields.
  - The "Save User" button must remain disabled until errors are resolved.

---

#### Step 2: API Integration

- **New User**
  - Trigger: `POST /api/users`
  - Condition: Form initialized via "New User" button.

- **Existing User**
  - Trigger: `PUT /api/users/{id}`
  - Condition: A user was selected from the table.

---

#### Step 3: Post-Submission Behavior

- **Success (200 OK / 201 Created)**
  - Display a success toast notification.
  - Refresh the User List Table.
  - If new user → clear the form.
  - If update → keep the form populated with updated values.

- **Error**
  - Display a global error notification at the top of the form.
  - Example: "Username already exists."

---

## 4.2 Data Persistence & Safety

### Unsaved Changes Protection

If the user:
- Modifies any field,
- Then attempts to click "New User",
- Or selects a different user,

The system must display a confirmation dialog:

> "Discard unsaved changes?"

Options:
- Confirm → Discard changes.
- Cancel → Stay on current form.

---

## 5. Edge Cases & Additional Behaviors

---

### 5.1 Pagination

- The table must support pagination.
- If total users > 10:
  - Display pagination controls at the bottom:
    - Previous
    - Next
    - Page Numbers
- Pagination must work with sorting and filtering.

---

### 5.2 Empty State

If:
- No users exist in the database, OR
- Filters return zero results,

Display a centered message inside the table:

> "No registered users found."

---

### 5.3 Loading State

- While fetching data from the API:
  - Display a loading spinner inside the table area.
  - Disable table interactions until loading completes.

---

## 6. Non-Functional Requirements

- UI must be responsive.
- Filtering and sorting must not cause full page reload.
- All user interactions must provide visual feedback.
- API errors must be handled gracefully.
