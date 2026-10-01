# `Via Foundry R SDK`

`viafoundry` is an R package for interacting with the Via Foundry API. It provides functions for user authentication, dynamic endpoint discovery, and executing API calls.

## Features
- **Authentication**: Log in to the Via Foundry API using personal access tokens (recommended) or username/password.
- **Dynamic API Discovery**: List all available API endpoints.
- **Custom API Calls**: Send HTTP requests to any endpoint with parameters or payloads.

---

## Installation

### Development Version
You can install the package directly from the source using `devtools`:

```R

# Install the viafoundry package
install.packages("viafoundry")

# Or

# Install devtools if not already installed
install.packages("devtools")

# Install the `viafoundry` package
devtools::install_github("viascientific/viafoundry-R-SDK")
```

## Getting Started

### Authentication

Before interacting with the API, you need to authenticate and store your credentials. 
Once authentication is done it will put token into `~/.viaenv` file, you don't need to re-authenticate.

#### Interactive Mode

Use the `authenticate()` function without arguments to be prompted for credentials interactively:

```R
library(viafoundry)
authenticate()
```

This will prompt you to:
1. Enter API hostname
2. Choose authentication method:
   - **Token** (recommended)
   - **Username and Password**

#### Using Token (Recommended)

Configure with a personal access token for enhanced security:

```R
library(viafoundry)

# Authenticate using token
authenticate_token(
    hostname = "https://your_foundry_server",
    token = "your-personal-access-token"
)

# Or use authenticate() with token parameter
authenticate(
    hostname = "https://your_foundry_server",
    token = "your-personal-access-token",
    config_path = "~/.viaenv",
    overwrite = TRUE
)
```

#### Using Username/Password

Alternatively, authenticate with username and password:

```R
library(viafoundry)

# Authenticate and save the token
authenticate(
    hostname = "https://your_foundry_server",
    username = "username",
    password = "password",
    config_path = "~/.viaenv",
    overwrite = TRUE
)
```

If your account uses multi-factor sign-in, also pass the 6 digit code from your authenticator app as `mfa_code = "123456"`, or a recovery code as `recovery_code = "..."`. In an interactive session you are prompted for the code when you leave both out. The stored token lasts 30 days, so you enter a code about once a month. If your account must set up multi-factor sign-in but has not yet, sign in once in a browser to add your authenticator app, then authenticate again.

### Configuration File
The viafoundry package uses a configuration file `(~/.viaenv)` to store the `hostname` and token. Example:

```R

{
    "hostname": "http://localhost",
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}

```

### Listing Available Endpoints

You can list all available API endpoints using the list_endpoints() function:

```R
library(viafoundry)
# Fetch and display available endpoints
endpoints <- discover()
print(endpoints)

```

### Calling an API Endpoint

To interact with a specific API endpoint, use the call_endpoint() function:


```R
library(viafoundry)
# Call an API endpoint
response <- call_endpoint(
    method = "GET",
    endpoint = "/api/v1/process",     # Replace with your desired endpoint
    params = list(key = "value"),   # Optional query parameters
    data = NULL                     # Optional payload for POST/PUT
)

# Print the API response
print(response)
```


### Example workflow

```R

library(viafoundry)

# Step 1: Authenticate (choose one method)

# Option A: Using token (recommended)
authenticate_token(
    hostname = "https://your_foundry_server",
    token = "your-personal-access-token"
)

# Option B: Interactive authentication
authenticate()

# Step 2: List all available endpoints
endpoints <- discover()
print("Available endpoints:")
print(endpoints)

# Step 3: Call a specific endpoint
response <- call_endpoint(
    method = "GET",
    endpoint = "/api/v1/process"
)
print("API response:")
print(response)


```

### Accessing the reports and load files

You can access any files in the report section using `SDK`
```R

library(viafoundry)
#get report id from Via Foundry

reportID <- 1 

# Fetch the report 
report <- fetchReportData(reportID)

# Get all processes belong to the report
getProcessNames(report)

# Select the file from a process or a module 
getFileNames(report, "RSEM_module")

# Load a specific file
rsem_data <- loadFile(report, "RSEM_module", "genes_expression_expected_count.tsv")
head(rsem_data)

```
You can access any files in the report section using `SDK`
```R

library(viafoundry)
#get report id from Via Foundry

reportID <- 1 

# Fetch the report 
report <- fetchReportData(reportID)

getAllFileNames(report)

# Choose a file from the list, if the extension is not tsv, csv, or txt, the file will be downloaded. you can define download directory

loadFile(report, "DE_module_RSEM", "control_vs_exper_des.Rmd")

```

### Uploading files to the reports section in Foundry

You can first get the available directories in report and upload the files to those directories. After 
creating new plots(e.g `png`, `pdf`) or would like to upload some analysis code to organize the data within the 
report section you can use this method.

```R

library(viafoundry)
#get report id from Via Foundry
reportID <- 1 

getReportDirs(1) # [1] "summary"       "multiqc"     "multiqc"  etc.

response <- uploadReportFile(1, "FILE_LOCATION", dir="summary") # Change the file location

print(response)
```

## Process Management Functionalities

### Process Functions

**List All Processes**
   - Fetches all existing processes from the API.
   - Example:
     ```R
     processes <- listProcesses()
     print(processes)
     ```

**Get Process Details**
   - Retrieves details for a specific process.
   - Example:
     ```R
     process_details <- getProcess("12345")
     print(process_details)
     ```

**Create a New Process**
   - Creates a new process using provided data. Make sure add all necessary parameters to create a process.
   - Example:
     ```R
     new_process <- createProcess(list(name = "New Process", description = "Example Process"))
     print(new_process)
     ```

**Update an Existing Process**
   - Updates a process with given data.
   - Example:
     ```R
     updated_process <- updateProcess("12345", list(name = "Updated Process"))
     print(updated_process)
     ```

**Delete a Process**
   - Deletes a process by its ID.
   - Example:
     ```R
     deleteProcess("12345")
     ```

**Duplicate a Process**
   - Duplicates an existing process.
   - Example:
     ```R
     duplicated_process <- duplicateProcess("12345")
     print(duplicated_process)
     ```

**Get Process Revisions**
   - Fetches all revisions for a given process.
   - Example:
     ```R
     revisions <- getProcessRevisions("12345")
     print(revisions)
     ```

**Check Process Usage**
   - Checks if a process is used in pipelines or runs.
   - Example:
     ```R
     usage <- checkProcessUsage("12345")
     print(usage)
     ```

### Menu Group Functions

**Create a Menu Group**
   - Creates a new menu group.
   - Example:
     ```R
     new_menu_group <- createMenuGroup("New Menu Group")
     print(new_menu_group)
     ```

**List Menu Groups**
   - Lists all menu groups.
   - Example:
     ```R
     menu_groups <- listMenuGroups()
     print(menu_groups)
     ```

**Update a Menu Group**
   - Updates a menu group name.
   - Example:
     ```R
     updated_menu_group <- updateMenuGroup("123", "Updated Menu Name")
     print(updated_menu_group)
     ```

### Parameter Functions

**List Parameters**
   - Fetches all parameters.
   - Example:
     ```R
     parameters <- listParameters()
     print(parameters)
     ```

**Create a New Parameter**
   - Creates a new parameter.
   - Example:
     ```R
     new_parameter <- createParameter(list(name = "New Param", type = "string"))
     print(new_parameter)
     ```

**Update a Parameter**
   - Updates an existing parameter.
   - Example:
     ```R
     updated_parameter <- updateParameter("123", list(name = "Updated Param"))
     print(updated_parameter)
     ```

**Delete a Parameter**
   - Deletes a parameter by its ID.
   - Example:
     ```R
     deleteParameter("123")
     ```

**Get Pipeline Parameters**
   - Retrieves parameter list for a pipeline.
   - Example:
     ```R
     pipeline_params <- getPipelineParameters("pipeline123")
     print(pipeline_params)
     ```


## Session History Functionality

### Save and Upload Session History

The `ViaFoundry SDK` for R allows you to save your session history while working in R. This is particularly useful for maintaining reproducibility and tracking the commands you execute.

### Saving Session History
To save your session history to a file with a timestamp, you can use the `prepareSessionHistory` function:

```R
# Save session history
history_file <- prepareSessionHistory()
print(paste("Session history saved to:", history_file))
```

### Uploading Session History
You can also upload your session history to the server.

#### Upload Session History Separately
```R
response <- uploadSessionHistory(report_id = "12345", dir = "session_logs")
print("Session History Upload Response:", response)
```

This functionality ensures that your session history is automatically saved and uploaded for reproducibility.


## Troubleshooting

### Common Issues

1.	Authentication Fails:
  -	Verify your `username`, `password`, and `hostname`.
  -	Check if the token in the `~/.viaenv` file is expired.
2.	API Call Fails:
  -	Ensure the endpoint exists and you have the necessary permissions.
  -	Check the API documentation for the correct method and parameters.
3.	Server Returns HTML Instead of JSON:
  -	Ensure the Accept: `application/json` header is sent with requests. This is handled automatically by the package.
4. If you want to re-authenticate remove the `viaenv` file (e.g `~/.viaenv`) 
