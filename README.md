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

2. install Amplify

        # might require sudo rights, and might take some time
        sudo npm install -g @aws-amplify/cli

        # verify installation
        amplify -v
        
3. connect to Amazon AWS, integrate GitHub, and Amazon Amplify

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
        
        
4. add amplify.yml to remote
    
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
    

