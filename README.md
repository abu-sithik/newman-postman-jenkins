![qbfzxipxudlnqyxdy5w0](https://user-images.githubusercontent.com/24310568/157593891-edf9e9ab-eaaa-4e74-8524-20b03a96274d.png)

# Newman-Jenkins
Integrating Postman/Newman API test collections with Jenkins and publish reports using Flock messenger

>  * Here, we will be exploring about how to run Postman collections on Jenkins with Freestyle Project. And we will try to generate our test run reports using Allure Jenkins plugin and will publish the test run status notifications to Flock messenger.* 

## Contents
* [Jenkins Setup](#jenkins)
 * [Step1:Install NodeJS plugin](#nodeJS)
 * [Step2:Add Newman and Allure Packages](#newmanAllure)
 * [Step3:Create a Newman job with freestyle.](#freestyle)
* [How to Run](#run)

## Jenkins Setup<a name="jenkins"></a>
Before we get started, you’ll need to install Jenkins on your machine. If you don’t have a setup Jenkins on your machine please follow this official [documentation](https://www.jenkins.io/doc/book/installing/).

After installing Jenkins, we need to configure NodeJS on Jenkins.

### Step1: Install [NodeJS plugin](https://plugins.jenkins.io/nodejs/) :<a name="nodeJS"></a>

`Go to → Manage Jenkins → Select Manage Plugins → Select Available tab and Search Active Choice Plugin.`
![Alt Text](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/vsyucb6zhr8xnwqy241r.png)

Install and restart the Jenkins for proper installation of the plugin.

### Step2: Add Newman and Allure Packages in NodeJS<a name="newmanAllure"></a>


`Open Jenkins: Manage Jenkins → Global Tool Configuration → NodeJS`

Set your compatible node version. We can set multiple NodeJS versions for multiple applications.

<span style="text-decoration: underline"> Make sure you follow these steps: </span>
- Check the “Install automatically” option
- Select compatible Node version (NodeJs-16.2.0)
- Add required packages in the “Global Packages to Install” section In our case, add the following packages newman newman-reporter-allure
- Save settings.

![Alt Text](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/b3t2n2k95ch5wv6xo2b9.png)

### Step3: Create a Newman job with freestyle.<a name="freestyle"></a>

**1. Create a Freestyle job to execute Newman collections scripts.**
	`Open Jenkins → New Item → Enter any job name→ Choose Freestyle Project → Click on Save button`

**2. Parameterising the Jenkins build for different Newman environment**
*A build parameter allows us to pass data into our Jenkins jobs. Build parameters are a powerful way to make any Jenkins job more dynamic.So by using build parameters, we can pass any data we want. In our use case, we will be passing the Newman environment variables. Note: Follow this [tutorial](https://docs.api.getpostman.com/), If you want to get a Newman environment variables’ unique id.*

[Extended Choice Parameter](https://plugins.jenkins.io/extended-choice-parameter/) Plug-In Installation:
- Go to 'Manage Jenkins' from the Jenkins homepage.
- Navigate to Manage Plugins
- Change the tab to Available
- Search for extended choice parameter
- Click “Download now and install after restart”

`General → Check “This project is parameterized” → Add parameter → Choice parameter → add respective choice values for parameterization [QA / Prod].`
![Alt Text](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/zvn2ce30cfd2p0610t2d.png)

`3.Source Code Management → Check “None”`
![Alt Text](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/yhmo9fllt3b580r48l54.png)

`4.Build environment → Check “Delete workspace before build starts”
5.Build environment → Check “Provide Node & npm bin/ folder to PATH” → select NodeJs name`

![Alt Text](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/notmdpf2njjaf21qove3.png)

`6.Build → Add build step → Select Execute Shell → And Paste the below code. → Save Your config.`

```
eg:- newman run [collections] -e [environment] -r allure --reporter-allure-export target/allure-results --suppress-exit-code 1
```

```
newman run "https://api.getpostman.com/collections/[collection_uid]?apikey=[apiKey]" \
    --environment "https://api.getpostman.com/environments/$server?apikey=[apiKey]" \ 
    -r allure --reporter-allure-export target/allure-results \ 
    -- suppress-exit-code 1
```

Note: If you want to run Newman via shell command using a unique collection id & API Key follow this [tutorial](https://docs.api.getpostman.com/). 
![Alt Text](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/rabuvyzxe2wvz2p5fcf6.png)

*Allure and Flock Plugin Installation*
- Go to 'Manage Jenkins' from the Jenkins homepage.
- Navigate to Manage Plugins
- Change the tab to Available
- Search for Flock and Check the box
- Search for Allure Report and Check the box
- Click “Download now and install after restart”

`7.Post Build Action → Allure Report → Add Path (“target/allure-results”) `
Allure Reports will be generated after every successful builds and report files are placed under the given $PATH
![Alt Text](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/3krw7o6qedcb9wbqomch.png)
`8.Post Build Action → Send Flock Notification (add flock webhook URL)`

*Please follow this guide to generate a Webhook URL*
- Go to https://apps.flock.com/?source=launcherbar
- Search for Jenkins
- Install and Configure to generate webhook URL.
![Alt Text](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/6eyahrec5ebpekxbpw9w.png)

### How to Run<a name="run"></a>

  Now we just have to build our job to execute our tests. And we can do so by clicking on the “Build with Parameters” button on the left side of the dashboard.

1. Go to Jenkins Job [jenkins_url/job/[#JobName]]
2. Click on Build with Parameter → Select the options that are required to be passed in the branch parameter [QA/ Prod] and click on → BUILD
![Alt Text](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/sea58fci6gwc64l03agx.png)

3. Flock messenger would notify you about the Jenkins build status.
![Alt Text](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/koefiespv4zg2r5tprcb.png)
4. To view reports click on “View” in flock → Will be redirected to Jenkins [After successful authentication]→ Click on Allure Report
![Alt Text](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/kivtk74tgo38c3o8m9p0.png)

> *In this article we have seen how to use Jenkins to run Newman tests, generate & publish reports using Allure / Flock plugins. I hope you may have found this useful. Cheers!!*

