## `validate` Function Features

### ServiceNowFormTask.validate
The `validate` function in the `ServiceNowFormTask` class and its subclasses is responsible for verifying that a task has been completed correctly. Here are its overall features:

1. **URL Validation**: 
   - Ensures the page is on the correct URL for validation.
   - Confirms the task is being validated in the right context.

2. **Field Change Detection**: 
   - Checks if any fields outside the scope of the task have been changed.
   - Ensures only the intended fields are modified.

3. **Record Retrieval**: 
   - Retrieves the record from the database using the `sys_id` stored in the session storage.
   - Ensures the record was actually created or edited.

4. **Record Existence Check**: 
   - Verifies that the record exists in the database.
   - Assumes the form was not submitted correctly if the record is not found.

5. **Field Value Validation**: 
   - Checks that the fields in the record match the expected values.
   - Ensures the task was completed as intended.

6. **Return Values**: 
   - Returns a tuple containing:
     - A score indicating the success of the task (1 for success, 0 for failure).
     - A boolean indicating whether the episode should end.
     - A message providing feedback on the task's completion.
     - A dictionary with additional information, such as error messages.

### KnowledgeBaseSearchTask.validate
The `validate` function in the `KnowledgeBaseSearchTask` class is specifically designed to validate knowledge base search responses. Here are its overall features:

1. **Chat Message Validation**:
   - Checks if there are chat messages and if the last message is from the assistant.
   - Returns failure if no answer was provided by the assistant.

2. **Answer Comparison**:
   - Converts both the provided answer and accepted answers to lowercase for case-insensitive comparison.
   - Checks the answer against both primary and alternative accepted answers.

3. **Return Values**:
   - Returns a tuple containing:
     - Score (1 for correct answer, 0 for incorrect)
     - Boolean for episode completion (true if correct, false if incorrect)
     - Success message for correct answers
     - Dictionary with validation message

4. **Flexible Answer Matching**:
   - Supports multiple acceptable answers through the `alternative_answers` list.
   - Uses partial string matching to allow for variations in answer formatting.

### FilterListTask.validate
The `validate` function in the `FilterListTask` class is designed to validate list filtering operations. Here are its overall features:

1. **URL Validation**:
   - Checks if the page is on the correct URL for validation using `check_url_suffix_match`.

2. **List Information Extraction**:
   - Extracts the current query from the list using `_extract_list_info`.

3. **Query Kind Validation**:
   - Determines if the filter is using AND or OR logic.
   - Checks if the filter kind matches the expected kind (AND/OR).

4. **Query Length Validation**:
   - Ensures the number of filter conditions matches the expected number.

5. **Query Columns Validation**:
   - Verifies that the filtered columns match the expected columns.

6. **Query Values Validation**:
   - Expands values to their display values for proper comparison.
   - Handles special cases like reference fields and choice fields.
   - Compares the filtered values with the expected values.

7. **Return Values**:
   - Returns a tuple containing:
     - Score (1 for correct filter, 0 for incorrect)
     - Boolean for task completion
     - Success message for correct filters
     - Dictionary with validation message

### ExtractListInfoTask.validate
The `validate` function in the `ExtractListInfoTask` class is responsible for validating the extraction of information from lists. Here are its overall features:

1. **Chat Message Validation**:
   - Checks if there's a valid assistant message with extracted information.

2. **Multi-field Validation**:
   - For tasks with more than two fields:
     - Parses the extracted JSON data.
     - Checks if all expected unique field values are present.
     - Validates the correctness of all extracted field values.

3. **Single-field Validation**:
   - For tasks with only one non-unique field:
     - Checks if the expected value is present in the assistant's message.

4. **Return Values**:
   - Returns a tuple containing:
     - Score (1 for correct extraction, 0 for incorrect)
     - Boolean for task completion
     - Success message for correct extractions
     - Dictionary with validation message

### SetProblemAsDuplicateTask.validate
The `validate` function in the `SetProblemAsDuplicateTask` class is responsible for validating that a problem has been correctly marked as a duplicate. Here are its overall features:

1. **Problem Record Retrieval**:
   - Retrieves both target and source problem records from the database using table API calls.
   - Verifies that both problems exist in the database.

2. **Duplicate Status Validation**:
   - Checks the `duplicate_of` field value for the target problem.
   - Converts the duplicate value from dict format to string ID when present.

3. **Ordering Validation**:
   - Has two validation modes based on `respect_problem_ordering` flag:
     - Strict mode: Only accepts target problem being marked as duplicate of source.
     - Flexible mode: Accepts either problem being marked as duplicate of the other.

4. **Comment Validation** (Optional):
   - When `add_comment` is true:
     - Verifies that the duplicated problem's description is set to "duplicate".
     - Checks comment in both problems if ordering is flexible.
     - Ensures comment is only added to the problem marked as duplicate.

5. **Return Values**:
   - Returns a tuple containing:
     - Score (1 for correct duplication, 0 for incorrect)
     - Boolean for task completion
     - Success message for correct duplication
     - Dictionary with validation message containing:
       - "Problem not found in DB" if problems missing
       - "Comment not added" if required comment missing
       - "Problem not marked as duplicate" if duplication failed
       - "Problem task was closed as duplicate" on success

### OrderHardwareTask.validate
The `validate` function in the `OrderHardwareTask` class is responsible for validating hardware orders in the service catalog. Here are its overall features:

1. **Configuration Validation**:
   - Validates each requested configuration item against the submitted options.
   - Handles different types of form elements:
     - Checkboxes
     - Radio buttons
     - Textareas

2. **Value Comparison**:
   - Uses `option_match_heuristic` helper function for comparison which:
     - Converts values to lowercase
     - Removes underscores and spaces
     - Performs exact string matching

3. **Type-Specific Validation**:
   - For checkboxes and radio buttons:
     - Performs exact match validation
   - For textareas:
     - Performs case-insensitive substring matching

4. **Return Values**:
   - Returns a tuple containing:
     - Score (1 for correct order, 0 for incorrect)
     - Boolean for task completion (always true)
     - Error message or success message
     - Dictionary with validation message

### DashboardRetrievalTask.validate
The `validate` function in the `DashboardRetrievalTask` class (and its subclasses) is responsible for validating dashboard data retrieval tasks. Here are its overall features:

1. **Chat Message Validation**:
   - Verifies that chat messages exist and contain valid responses.
   - Checks if the last message is from the assistant.

2. **Chart Data Extraction**:
   - Retrieves chart data using `_get_chart_by_title`.
   - Handles both single and multi-series charts.
   - Processes chart data based on configuration:
     - Extracts specific series by name
     - Handles different chart types (bar, column, spline)

3. **Data Processing**:
   - Cleans and normalizes chart data:
     - Removes None percent values
     - Strips trailing spaces from labels
     - Handles label selection based on context
     - Validates label uniqueness

4. **Value Validation**:
   - Compares extracted values against expected values.
   - Supports different types of questions:
     - Value retrieval
     - Min/max identification
     - Mean/median/mode calculations

5. **Return Values**:
   - Returns a tuple containing:
     - Score (1 for correct retrieval, 0 for incorrect)
     - Boolean for task completion
     - Success or error message
     - Dictionary with validation details