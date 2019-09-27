# BlueantJS

BlueAnt unofficially offers a rest service (see WEB-INF/conf/axis2.xml)
Our service provider does not give any support for it and won't garantee data consistency.

They instead recommend to use the soap service. However..

## How to use the rest service
The rest service has the same endpoints as the soap service (see [wsdl2html.html](https://github.com/sinnerschrader/BlueantJS/blob/master/wsdl2html.html))

For example you can call _https://{blueantHost}/services/BaseService/Login_ with username and password as get/post parameters.
As a result you will get a session key which you have to add to all other requests.
Your personally assigned projects can be requested with _https://{blueantHost}/services/WorktimeAccountingService/getProjects_ and only the 
sessionID as post parameter.

> But, dont use GET-Parameters for security reasons. You have to pass your arguments as form data

One thing to note: BlueAnt does not return json answers, but only XML.

### Test it
* Check out this project
* cd demo
* npm install
* npm start
* Open http://localhost:3000

## Library
This project offers a helper library to use with blueant (see [src/blueant.ts](https://github.com/sinnerschrader/BlueantJS/blob/master/src/blueant.ts)).

### Features
* Handling of request and responses from blueant
* Relogin after session timeout
* Promises

### Documentation
**setEnviroment(enviromentUrl: string): void**

Baseurl of all ajax requests

**login(username: string, password: string): Promise\<Blueant\>**

Login

**isAuthenticated(): boolean**

Returns wether the user is authenticated.
Does not return wether the session is still active (but the session will be reactivated by the library).

**logout(): void**

Logout

**getProjects(): Promise\<Project[]\>**

Returns an array of projects that you are assigned to.

**getTasks(projectID: number): Promise\<Task[]\>**

Returns an array of tasks.

Hint: Tasks can have child tasks and they have a boolean attribut _worktimeAllowed_.

**getActivities(): Promise\<Activity[]\>**

Returns a list of activities like "Productive" or "Night work".

**addWorktime(date: Date, projectID: number, taskID: number, activityID: number, billable: boolean, duration: number, comment: string): Promise\<any\>**

**editWorktime(worktimeID: number, date: Date, projectID: number, taskID: number, activityID: number, billable: boolean, duration: number, comment: string): Promise\<any\>**

Add or edit worktimes.

**deleteWorktime(worktime: Worktime): Promise\<any\>**

Deletes a worktime entry.

**getWorktimes(fromDate: Date, toDate?: Date): Promise\<Worktime[]\>**

Returns a list of worktimes. The toDate parameter is optional and one day as period is assumed if left out.

**getHolidays(fromDate: Date, toDate?: Date): Promise\<Holiday[]\>**

Returns a list of holidays. If the second parameter is undefined, one year as period will be assumed.

### Example Usage

#### Startup
If you build a javascript application you may be limited to the same origin policy.

You can easily create a proxy for blueant with express and express-http-proxy.

```javascript
app.use('/blueant', proxy('https://blueant-uat.sinnerschrader.com', {
    forwardPath: function (req, res) {
        var path = require('url').parse(req.url).path;
        return '/blueant' + path;
    }
}));
```

#### Login
```javascript
var blueant = new Blueant();
blueant.setEnviroment('http://localhost:3000/blueant');
blueant.login('username', 'password').then(() => { console.log('Login successfull'); });
```
Have a look at demo/app/login/login.component.ts

#### Worktimes
```javascript
var blueant = new Blueant();
blueant.setEnviroment('http://localhost:3000/blueant');
blueant.login('username', 'password').then(() => {
    console.log('Login successfull');
    blueant.getWorktime(new Date()).then(function (worktimes) {
        console.log(worktimes);
    });
});

```

#### Further examples
You can find many examples in the demo application.
Have a look at the components in demo/app/.

### Build
Any changes in the src-folder can be build with the following command:

```bash
npm run build
```





