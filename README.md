# amplify-ci-cd-guide  
The guide will guide through usage of Amazon Amplify via GitHub integration.  


## 0th Step: Requirements  
1. Credentials
    - An Amazon AWS account  
    - Administrator rights on Linux environment  
2. Software
    - Node.js v10.x or later  
    - npm v5.x or later  
    - git v2.14.1 or later  

## 1st Step: First Working Build
1. Front-End Branch Creation
    - create feature branch named `front-end`

        	# clone the repository you created
        	git clone <repo address>
        
        	# in case you took your time after cloning, do synchronize your branch
        	git pull
        
        	# create a new branch, where we would do our first step, front-end code
        	git checkout -b front-end
        	git push origin front-end
            git push --set-upstream origin front-end
        
        	# verify branch structure by the following command,
        	# 	you should be able to see `front-end` branch
        	git branch -a

    - create folder layout, and add hello-world html file
    
            # create folder layout
            mkdir front-end
            cd front-end
            mkdir static
            cd static
            
            # create a static HTML file
            echo "This is the first step of guide, <br> in which we would see a plain HTML file served with no functionality." > index.html
            
            # push changes
            cd ../../
            git add front-end
            git commit -m "[FE]: first static code"
            git push

        
2. connect to Amazon AWS, integrate GitHub, and Amazon Amplify

    - login into Amazon AWS
    - navigate to Amazon Amplify Console
    - click `Get Started` button under `Deploy`
    - select `GitHub`, select `Continue` and login to your GitHub to give access rights of your repository to Amazon Amplify
    - from 'Select a repository', pick your newly created repo
        - in our case, it is `ulfet/amplify-ci-cd-guide`
    - from 'Select a branch', pick your feature branch, and click `Next`
        - in our case, it is `front-end`
        - after we set up our pipeline, we would switch to a different branching strategy
    - click `Download` to retrieve your clean amplify.yml file, and click `Next`
        - we would fill it in later
    - click `Save and Deploy`
        - this deployment would not display any website because we did not add our amplify.yml file to our repository yet.
        
    [image/aws_6.png]
    Click on the thumbnail to see failure:
    [image/aws_7.png]
        
        
3. add amplify.yml to remote
    
        # make sure that you are on the same branch as Amplify watches (that we set in the previous step)
        git branch

        # copy the amplify.yml we downloaded into project root
    
    The content of amplify.yml should be as follows:
    
        version: 1
        frontend:
          phases:
            # IMPORTANT - Please verify your build commands
            build:
              commands: []
          artifacts:
            # IMPORTANT - Please verify your build output directory
            baseDirectory: ./front-end/static
            files:
              - '**/*'
          cache:
            paths: []
    
    Note that we updated baseDirectory to where our index.html resides, without a trailing slash, as we noted below.  
    The rest of the file is left as it is.  
    By changing this directive, we are telling Amplify to serve files stored in the path, where we created our index.html file.  
    That is why previous deployment failed, and after pushing our changes, why next deployment would success.
    
            baseDirectory: ./front-end/static
        
    Let us push this file into our feature branch
            
            # control the branch you are in
            git branch
            
            # push
            git add amplify.yml
            git commit -m "[AMP]: amplify.yml initial file added"
            git push
            
    This would cause Amplify to initiate another build, yet this time, we would be able to see our front-end hello world page.  
    [image/aws_8.png]
    Click on the thumbnail on the left, and you would see a webpage like this:  
    [image/aws_9.png]
    
    The gains from this step:
    1. usage of Amplify web interface,
    2. integration of GitHub,
    3. first front-end code publish
    4. amplify.yml file
    

## 2nd Step: Initialize Amplify

1. install Amplify

        # might require sudo rights, and might take some time
        sudo npm install -g @aws-amplify/cli

        # verify installation
        amplify -v

2. configure amplify 

    with the following command:

        amplify configure

    expect:

        Specify the AWS Region: eu-central-1
        Username: amplify-backend
        (follow the steps in the opened browser tab afterwards until success)

    at the last page, to keep it safe, download credentials via `Download .csv`. This is the only time we have direct access to `Secret access key`, which we would use.

    Return back to the terminal, and press enter.

    expect:

        ? accessKeyId:  ********************
        ? secretAccessKey:  ****************************************
        This would update/create the AWS Profile in your local machine
        ? Profile Name:  default

        Successfully set up the new user.


3. initialize Amplify

    - create feature branch named `back-end`

            # clone the repository you created
            git clone <repo address>
        
            # in case you took your time after cloning, do synchronize your branch
            git pull
        
            # create a new branch, where we would do our first step, front-end code
            git checkout -b back-end
            git push origin back-end
            git push --set-upstream origin back-end
        
            # verify branch structure by the following command,
            #   you should be able to see `back-end` branch
            git branch -a

    - run the following command:

            amplify init --appId d3ppw31367knnz

    - expect:

          Note: It is recommended to run this command from the root of your app directory
            ? Enter a name for the project amplifycicdguide
            ? Enter a name for the environment dev
            ? Choose your default editor: Visual Studio Code
            ? Choose the type of app that you're building javascript
            Please tell us about your project
            ? What javascript framework are you using none
            ? Source Directory Path:  src
            ? Distribution Directory Path: dist
            ? Build Command:  npm run-script build
            ? Start Command: npm run-script start
        
            (...)

            ? Do you want to use an AWS profile? Yes
            ? Please choose the profile you want to use: default

            (wait till it finishes)

    - now you would see amplify folder initialized in the project root:

            ~/Desktop/amplify-ci-cd-guide$ ls
            amplify  amplify.yml  front-end  README.md  src

    - validate installation via:

          amplify status

    - expect:

            Current Environment: dev
    
            | Category | Resource name | Operation | Provider plugin |
            | -------- | ------------- | --------- | --------------- |


## 3rd Step: Add an API to AWS Project  

In this step we are going to add GraphQL API to our AWS project.  

1. add GraphQL API
    
    - run:

            git add api

    - expect:
    
            ? Please select from one of the below mentioned services: GraphQL
            ? Provide API name: amplifycicdguide
            ? Choose the default authorization type for the API API key
            ? Enter a description for the API key: trial 
            ? After how many days from now the API key should expire (1-365): 7
            ? Do you want to configure advanced settings for the GraphQL API No, I am done.
            ? Do you have an annotated GraphQL schema? No
            ? Choose a schema template: Single object with fields (e.g., “Todo” with ID, nam
            e, description)

            The following types do not have '@auth' enabled. Consider using @auth with @model
                 - Todo
            Learn more about @auth here: https://docs.amplify.aws/cli/graphql-transformer/directives#auth


            GraphQL schema compiled successfully.
    
            Edit your schema at /home/ulfet/Desktop/amplify-ci-cd-guide/amplify/backend/api/amplifycicdguide/schema.graphql or place .graphql files in a directory at /home/ulfet/Desktop/amplify-ci-cd-guide/amplify/backend/api/amplifycicdguide/schema
    
            ? Do you want to edit the schema now? Yes
            
            Please edit the file in your editor: /home/ulfet/Desktop/amplify-ci-cd-guide/amplify/backend/api/amplifycicdguide/schema.graphql
            Successfully added resource amplifycicdguide locally
    
    - your preferred editor opens here, save what is recommended, which is as follows.
    
            type Todo @model {
              id: ID!
              name: String!
              description: String
            }
            
    - exit the editor.

5. push your changes

		git add amplify/
        git commit -m "[AMP]: Amplify init files added with GraphQL"
        git push


6. make changes on AWS Console (from browser)

    - do note that AWS does not keep track of our current branch.
    We have to set it so it also watches our `back-end` branch.

    - pick branch as `back-end`
    - pick backend environment as `dev`
    - click `Save and deploy`
    - on the redirected page, click `Run build` to start the build on the `back-end` branch

    Wait until build process finishes.

    Run the following command to see what happened:

        amplify console api

    One would observe that there are no GraphQL initialized yet. This is because we did not add any back-end configuration to our amplify.yml.
    Let's do that.  

    Edit the amplify.yml file so that it looks like this:

		version: 1
			backend:
			  phases:
			    build:
			      commands:
			        - amplifyPush --simple
			        
			frontend:
			  phases:
			    # IMPORTANT - Please verify your build commands
			    build:
			      commands: []
			  artifacts:
			    # IMPORTANT - Please verify your build output directory
			    baseDirectory: ./front-end/static
			    files:
			      - '**/*'
			  cache:
			    paths: []


	add amplify.yml file to remote so that another build is initialized.

		git add amplify.yml
		git commit -m "[AMP]: back-end init code added to amplify.yml"
		
	You would see that build has failed due to credential error.  
	Follow these steps:
            
            On Amplify console, click `General`.  
            From `App details`, click `Edit`.

            Pick service role as `amplifyconsole-backend-role`.

            Click save.

            Go to back-end and click on `Redeploy this version`.
            
    After seeing build go green, one can run:
    
            amplify console api
            
    Yet, this time one can access to GraphQL API from AWS Console.
	

    In case no service role can be selected, go to go to https://console.aws.amazon.com/iam/home?#/roles and add a user there.