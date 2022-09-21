# Trigger Jenkins Pipeline Job by SCM Change

## Setting up your Github
- Go to your **Repository -> Setting -> Webhooks**
- Add Webhook, fill in your Payload URL with your Jenkins URL and add */github-webhook/* behind and choose "Just the push event"
![trigger-jenkins](/images/trigger-2.png)
- Save it 
![trigger-jenkins](/images/trigger-3.png)

## Configure your Jenkins project
- Open your project 
- Click "Configure" 
- Under Build Trigger, Check 'GitHub hook trigger for GITScm polling"
![trigger-jenkins](/images/trigger-4.png)
- Save it