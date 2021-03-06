## SQLFlow Job API tutorial

This article describes how to use the Job Rest API provided by the SQLFlow to 
communicate with the SQLFlow server and export the data lineage in json, csv, graphml formats.

### Prerequisites
To use the Rest API of the SQLFlow, you need to <a href="https://gudusoft.com">obtain a premium account</a>. 
After that, you will get the `userid` and `secret key`, which will be used in the API.

- User ID
- Secrete Key

#### Generate a token

Once you have the `userid` and `secret key`, the first API need to call is:

```
/gspLive_backend/user/generateToken
```

This API will return a temporary token that needs to be used in the API call thereafter.

```
curl -X POST "https://api.gudusoft.com/gspLive_backend/user/generateToken" -H  "Request-Origion:testClientDemo" -H  "accept:application/json;charset=utf-8" -H  "Content-Type:application/x-www-form-urlencoded;charset=UTF-8" -d "secretKey=YOUR SECRET KEY" -d "userId=YOUR USER ID HERE"
```

More detail, please see https://github.com/sqlparser/sqlflow_public/edit/master/api/readme.md

### Call Rest API

#### 1. Submit a sqlflow job

Call this API by sending the SQL files and get the result includes the data lineage. SQLFlow job supports both of multiple files and zip archive file.

```
/gspLive_backend/sqlflow/job/submitUserJob
```

Example in `Curl`
```
curl -X POST "https://api.gudusoft.com/gspLive_backend/sqlflow/job/submitUserJob" -H "accept:application/json;charset=utf-8" -H "Content-Type:multipart/form-data" -F "userId=YOUR USER ID HERE" -F "token=YOUR TOKEN HERE" -F "sqlfiles=@FIRST FILE PATH" -F "sqlfiles=@SECOND FILE PATH" -F "dbvendor=dbvmssql" -F "jobName=job1"
```

**Note:**
 * **-H "Content-Type:multipart/form-data"** is required
 * Add **@** before the file path
 
Return data: 
```json
{
	"code":200,
	"data":{
		"jobId":"c359aef4bd9641d697732422debd8055",
		"jobName":"job1",
		"userId":"google-oauth2|104002923119102769706",
		"dbVendor":"dbvmssql",
		"dataSource":{
			
		},
		"fileNames":["1.sql","1.zip"],
		"createTime":"2020-12-15 15:14:39",
		"status":"create"
	}
}
```

Please records the jobId field.

### 2. Get job status

 * Get the specify user job status and summary
  
  ```
  /gspLive_backend/sqlflow/job/displayUserJobSummary
  ```
  
  Example in `Curl`
  
  ```json
  curl -X POST "https://api.gudusoft.com/gspLive_backend/sqlflow/job/displayUserJobSummary" -F "jobId=c359aef4bd9641d697732422debd8055" -F "userId=YOUR USER ID HERE" -F "token=YOUR TOKEN HERE"
  ```
  
  Return data:
  ```json
  {
	"code":200,
	"data":{
		"jobId":"c359aef4bd9641d697732422debd8055",
		"jobName":"job1",
		"userId":"google-oauth2|104002923119102769706",
		"dbVendor":"dbvmssql",
		"dataSource":{
			
		},
		"fileNames":["1.sql","1.zip"],
		"createTime":"2020-12-15 15:14:39",
		"status":"success",
		"sessionId":"fe5898d4e1b1a7782352b50a8203ca24c04f5513446e9fb059fc4d584fab4dbf_1608045280033"
	}
  }
  ```
  
 * Get all jobs (include history jobs) status and summary
 
  ```
  /gspLive_backend/sqlflow/job/displayUserJobsSummary
  ```
 
  Example in `Curl`
  
  ```json
  curl -X POST "https://api.gudusoft.com/gspLive_backend/sqlflow/job/displayUserJobsSummary" -F "userId=YOUR USER ID HERE" -F "token=YOUR TOKEN HERE"
  ```
  
 

### 3. Export data lineage
 
 When the job status is **success**, you can export the data lineage in json, csv, graphml formats

 * 3.1 Export data lineage in json format
  
  ```
  /gspLive_backend/sqlflow/job/exportLineageAsJson
  ```
  
  Example in `Curl`  
  
  ```
  curl -X POST "https://api.gudusoft.com/gspLive_backend/sqlflow/job/exportLineageAsJson" -F "userId=YOUR USER ID HERE" -F "token=YOUR TOKEN HERE" -F "jobId=c359aef4bd9641d697732422debd8055" --output lineage.json
  ```
  **Note:**
   > If you want to get table to table relation, please add option -F "tableToTable=true"
 
 * 3.2 Export data lineage in csv format
  
  ```
  /gspLive_backend/sqlflow/job/exportLineageAsCsv
  ```
  
  Example in `Curl`  
  
  ```
  curl -X POST "https://api.gudusoft.com/gspLive_backend/sqlflow/job/exportLineageAsCsv" -F "userId=YOUR USER ID HERE" -F "token=YOUR TOKEN HERE" -F "jobId=c359aef4bd9641d697732422debd8055" --output lineage.csv
  ```
  
  **Note:**
   > If you want to get table to table relation, please add option -F "tableToTable=true"
  
 
 * 3.3 Export data lineage in graphml format, you can view the lineage graph at yEd Graph Editor
   
  ```
  /gspLive_backend/sqlflow/job/exportLineageAsGraphml
  ``` 
  
  Example in `Curl`  
  
  ```
  curl -X POST "https://api.gudusoft.com/gspLive_backend/sqlflow/job/exportLineageAsGraphml" -F "userId=YOUR USER ID HERE" -F "token=YOUR TOKEN HERE" -F "jobId=c359aef4bd9641d697732422debd8055" --output lineage.graphml
  ```
  
  **Note:**
   > If you want to get table to table relation, please add option -F "tableToTable=true"
