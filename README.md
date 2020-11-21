# exactius-netlify
Holds all netlify sites

## Setup for development
- Clone the repo in the file directory you want the code to live
- In your terminal, navigate to the `exactius-netlify` repo that has been cloned
- Run `npm install` (if you got any errors, reach out to someone on the production team to resolve)
- Once the install runs without any errors fetch all branches `git fetch`
- Checkout the remote branch related to the client you are working on, for example: `git checkout --track origin/workmoney` (if you are working on a new client see below)
- Checkout a feature branch off of the client production branch, for example `git checkout workmoney-election-lp`
- Run the build command related to the client you are working on (can be found in `package.json`), for example `npm run build:workmoney`
- Either create a new HTML page to be worked on or run the dev server on an existing page `npm run dev [client-directory/page.html]`, for example `npm run dev workmoney/index.html`
- The page should be running at `http://localhost:1234`

## Deploying a page
- Netlify handles continuous deployment when a client-specific production branch is pushed. 
- Before development, checkout the client specific branch you are going to develop on `git checkout workmoney`
- Checkout a feature branch off of the production branch `git checkout workmoney-election-lp`
- When development is done and you are ready to launch, create a pull request for your branch (your branch will need to be approved by one other person)
- Upon approval, merge your pull request into the client production branch (please squash your commits)
- Netlify will build your branch automatically
- When deployment is complete be sure to check and QA your branch that everything has successfully deployed

## Setting up a new client
### Repo setup
- On the master branch, create a new folder for the client in the root directory (ex. /client-name)
- Add the following folders to the new client folder: `images`, `scripts`, `styles`
- Add a simple `index.html` page to the new client directory
- In the `styles` folder, add `main.scss` and add `@import '../../global/base.scss';` to the top. This stylesheet adds reset styles and other global styles and should be added to the `index.html` and any other pages that are added and all other stylesheets for this client should be imported here.
- In `package.json` add a new line for your client adding the build command (`"build:client-name": "parcel build client-name/*.html"`)
- Push this all up to origin on master
- Create a branch for the client (ex `client-name`) and push that new branch to origin

### Domain Setup
- On Netlify, when you login, if you don't see a subdomain for the client you are creating on the initial list, then speak to a Github admin or the Product team to set this up for you.
- If you do see a subdomain for your client on the list, click the subdomain you want to build to
- Click `Domain Settings` then at the top click `Add Domain Alias` and enter in the subdomain you want (if unsure, speak with the Product team)
- Give this new domain alias to the Product team to set up a CNAME Record on Cloudflare if we have access or to push to the client

### Deployment Setup
- In Netlify, click `Builds & Deploy`
- Edit the Build Settings for the build command to be the command you entered into the `package.json` earlier `npm run build:client-name`
- Edit the Publish Directory to be `dist`
- Click Save
- Beneath Build Settings, edit the `Deploy Contexts`
- Add your new client branch name to the `Production Branch` input
- On `Branch Deploys` choose `None`
- Click Save


## Forms
Netlify has form submission aggregation out of the box on their UI. We need to structure our forms to also submit to this UI.
- All forms should have `data-netlify="true"` data attribute and a `name` attribute on the parent `<form>` element. Without this the form will not send to Netlify.
- Netlify has a default `action` at the end of the form submission. We will most likely want to override this so all form submissions will probably have to be handled in the JS, but we have fetch implemented to make this easy. Here is an example of how to handle the form submission in the JS:
```
    e.preventDefault()

    document.getElementById('form-default')

    fetch('/', {
      method: 'POST',
      body: new FormData(formDefault)
    }).then(() => {
      // whatever you want to do after form submission
    })
```
- If you are only submitting the form to Netlify and no where else, there is no need to create an endpoint, just put the root url (`/`) for the action in the fetch function
- All inputs will be compiled by Netlify and reflected in the form. Including hidden inputs and whatever else you need.
- Any checkbox groups should be contained in the `<fieldset>` object and every checkbox input should have a name attribute in the following format `string[]`. This tells the JS to put all of the answers in an array to be sent to Netlify
- All radio button groups should also be contained in the `<fieldset>` object and every radio button should have the same name (can be a normal string) to have the questions and answers set up correctly. 
- For more information you can visit here: https://docs.netlify.com/forms/setup/

## Rules of the Repo
- All development must be done on a seperate, new, clean branch and a PR must be done before we merge anything (DO NOT develop on master or on one of the client specific production branches)
- `base.scss` and `reset.scss` should only be updated with global styles that would be needed over all clients
- All forms MUST have `data-netlify="true"` on the `<form>` HTML element to collect form results in the Netlify UI
