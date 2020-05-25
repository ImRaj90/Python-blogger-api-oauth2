# Python-blogger-api-oauth2
Working with Blogger API v3 using python for beginners via Oauth 2.0
Hi Guys,

I’m going to show you how to connect Google blogger API using python. I had a requirement of creating more than 100 plus blog pages by duplicating existing a single blog page. Basically,
I need to copy the content from already publisher blog page
Creating a new page
Paste the content of the existing blog page to a new blog page with minor changes
Working with Blogger API v3 using python for beginners via Oauth 2.0

The main reason for posting this, I couldn't find the right tutorial that can help in understanding the code in one go and it took a lot of time for time to complete the task. this and so many people were asking for the answer. I hope this tutorial will help you.

Before going into this, if there are any mistakes in the tutorial or if I didn’t follow best practices. Please apologize, I started learning python from last 2 weeks, I have completed programming-essentials-in-python(4 modules free learning) from the Cisco website. It's available for free. You can go check that,

 


There are two ways, you can interact with your blog using python:
Oauth 2.0
API key
In this blog, I will show how we can interact with blogger API using python with Oauth 2.0. It is a recommended way to interact with your Google API resources. The first step will be creating project and credentials

In order to access your enabled APIs(i.e, Blogger API in our case), we need to create credentials, these credentials must be kept in secret and otherwise, someone may steal your keys, you will end up paying for Google once the quota is exceeded. Please follow the below steps to create credentials for your project. Steps are in the below, video you can use it for creating client_secret.json file.
Open the Google API Console Credentials page.
Click Select a project, then NEW PROJECT, and enter a name for the project, and optionally, edit the provided Project ID. Click Create.
On the Credentials page, select Create credentials, then OAuth client ID.
You may be prompted to set a product name on the Consent screen; if so, click Configure consent screen, supply the requested information, and click Save to return to the Credentials screen.
Select Web Application for the Application Type. Follow the instructions to enter JavaScript origins, redirect URIs, or both.
Click Create.
On the page that appears, copy the client ID and client secret to your clipboard, as you will need them when you configure your client library. 



After creating the project, you need to enable the Blogger API v3 in the Google API console. Please follow the below steps:
Navigate to APIs & Services
Search for Blogger API
Enable it



Your client-secrets should look like below:
{"web":{"client_id":"814572920180-5lua2cehvonjidhjck5sql4gf2roavu5.apps.googleusercontent.com","project_id":"wordpress-learning-203906","auth_uri":"https://accounts.google.com/o/oauth2/auth","token_uri":"https://oauth2.googleapis.com/token","auth_provider_x509_cert_url":"https://www.googleapis.com/oauth2/v1/certs","client_secret":"R0B_Ayy6A-PBhRIJ5nhUmFOe","redirect_uris":["http://localhost:8090/"]} 

Note, after downloading the client_secret.json file, make sure that the name should be as client_secret.json and place it in the folder wherever your current project is. My project folder is "E:\blogger api\python-blogger"




Open the command prompt and run as an administrator, navigate to the project folder, mine is "E:\blogger api\python-blogger" and install below libraries:
pip install --upgrade google-api-python-client
pip install httplib2
pip install oauth2client

After installing libraries, copy the below the code and save it in your project folder and save the file with .py extension,

import httplib2
from oauth2client.client import flow_from_clientsecrets
from oauth2client.file import Storage
from oauth2client.tools import run_flow
from googleapiclient import discovery
CLIENT_SECRET = 'client_secret.json'
SCOPE = 'https://www.googleapis.com/auth/blogger'
STORAGE = Storage('credentials.storage')
# Start the OAuth flow to retrieve credentials
def authorize_credentials():
# Fetch credentials from storage
    credentials = STORAGE.get()
# If the credentials doesn't exist in the storage location then run the flow
    if credentials is None or credentials.invalid:
        flow = flow_from_clientsecrets(CLIENT_SECRET, scope=SCOPE)
        http = httplib2.Http()
        credentials = run_flow(flow, STORAGE, http=http)
    return credentials
credentials = authorize_credentials()
print(credentials)

*note: please take care of the indentation.

After saving it, run the python program, then you will see a browser will ask to login into your Google account and request to grant the permission. Once you have accepted it. You should see the message authentication successful, message on the command prompt


Once the authentication is successful, credentials.storage file will be created in the project folder, please refer to the below screenshot.



Credentials.storage file contains all information required for authentication, which prevents the program from authenticating again every time we are running the program. This is one time set up.

Then, create a function as below:
def get_pages_from_jobs(blogId):
    credentials = authorize_credentials()
    http = credentials.authorize(httplib2.Http())
    discoveryUrl = ('https://www.googleapis.com/blogger/')
    service = discovery.build('blogger', 'v3', http=http, discoveryServiceUrl=discoveryUrl) 
At the end of the program, we need to call the above function(below highlighted in red). After the function, the code should look like this:

import httplib2
from oauth2client.client import flow_from_clientsecrets
from oauth2client.file import Storage
from oauth2client.tools import run_flow
from googleapiclient import discovery
CLIENT_SECRET = 'client_secret.json'
SCOPE = 'https://www.googleapis.com/auth/blogger'
STORAGE = Storage('credentials.storage')
# Start the OAuth flow to retrieve credentials
def authorize_credentials():
# Fetch credentials from storage
    credentials = STORAGE.get()
# If the credentials doesn't exist in the storage location then run the flow
    if credentials is None or credentials.invalid:
        flow = flow_from_clientsecrets(CLIENT_SECRET, scope=SCOPE)
        http = httplib2.Http()
        credentials = run_flow(flow, STORAGE, http=http)
    return credentials
credentials = authorize_credentials()
print(credentials)
def get_pages_from_jobs(blogId):
    credentials = authorize_credentials()
    http = credentials.authorize(httplib2.Http())
    discoveryUrl = ('https://www.googleapis.com/blogger/')
    service = discovery.build('blogger', 'v3', http=http, discoveryServiceUrl=discoveryUrl)
    
get_pages_from_jobs(xxxxxxx)

After that, you can copy the below code, you can copy the respective code for corresponding actions:
1. For users
Add the below code(highlighted in yellow) just below  service = discovery.build('blogger', 'v3', http=http, discoveryServiceUrl=discoveryUrl)
users = service.users()
# Retrieve this user's profile information
thisuser = users.get(userId='self').execute()
print('This user\'s display name is: %s' % thisuser['displayName'])

2. For Listing for blogs
For listing the blogs, we need to add the below code(highlighted in yellow) along with the above 1st action, just below print('This user\'s display name is: %s' % thisuser['displayName'])
blogs = service.blogs()
# Retrieve the list of Blogs this user has write privileges on
thisusersblogs = blogs.listByUser(userId='self').execute()
for blog in thisusersblogs['items']:
print('The blog named \'%s\' is at: %s' % (blog['name'], blog['url']))

3. For posts associated with specific users
For listing the blogs, we need to add the below code(highlighted in yellow) along with the above 1st, 2nd action, Add the below code(highlighted in yellow) just below  print('The blog named \'%s\' is at: %s' % (blog['name'], blog['url']))
posts = service.posts()
# List the posts for each blog this user has
for blog in thisusersblogs['items']:
print('The posts for %s:' % blog['name'])
request = posts.list(blogId=blog['id'])
while request != None:
posts_doc = request.execute()
if 'items' in posts_doc and not (posts_doc['items'] is None):
for post in posts_doc['items']:
print(' %s (%s)' % (post['title'], post['url']))
request = posts.list_next(request, posts_doc)

4. For getting a specific page using ID
This code independent of the above 1,2,3 actions, you can just the below code(highlighted in yellow) directly just below the  service = discovery.build('blogger', 'v3', http=http, discoveryServiceUrl=discoveryUrl)
page=service.pages()
onepage=page.get(blogId=’xxxxx’,pageId=’xxxxx’).execute()
print("this will show specific onepage", onepage)

5.For creating multiple posts/pages(in drafts)
This code independent of the above 1,2,3 actions, you can just the below code(highlighted in yellow) directly just below the  service = discovery.build('blogger', 'v3', http=http, discoveryServiceUrl=discoveryUrl)
cities=['canada','singapore','germany','italy']
for item in cities:
title="title something"+item
payload={
"content": "<div dir=\"ltr\" style=\"text-align: left;\" trbidi=\"on\">\nEspeciallySports is a sports news portal aims at bringing the latest sports news for the fans that cover MMA, WWE, UFC, Cricket, Football, etc&nbsp;</div>\n",
"title": title
}
respost=page.insert(blogId='xxxx',body=payload,isDraft=True).execute() #publishing the new post
print("printing the page id:",respost['id'])

6. For updating the existing post with ID
This code independent of the above 1,2,3 actions, you can just the below code(highlighted in yellow) directly just below the  service = discovery.build('blogger', 'v3', http=http, discoveryServiceUrl=discoveryUrl)
payload={
"content": "<div dir=\"ltr\" style=\"text-align: left;\" trbidi=\"on\">\nEspeciallySports is a sports news portal aims at bringing the latest sports news for the fans that cover MMA, WWE, UFC, Cricket, Football, etc&nbsp;</div>\n",
"title": title
}
respost=page.update(blogId=’xxxxxx’, pageId=’xxxxx’, body=payload, publish=True).execute() #updating the existing post/page

I hope this post is useful! I'm still a beginner, please comment and let me know if anything is missing here
