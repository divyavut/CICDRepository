## CI/CD Angular project workflow

### CodePipeline Triggers CodeBuild:

1. When a new commit is pushed to the GitHub repository, CodePipeline detects the change and triggers the CodeBuild project associated with the pipeline.

### CodeBuild Environment Initialization:

2. CodeBuild spins up a new build environment based on the configuration specified (e.g., Ubuntu, Node.js version).

### Source Code Checkout:

3.  CodeBuild automatically checks out the source code from the specified GitHub repository into a temporary directory in the build environment. This is typically done in the install phase of the build process.

### Build Process:

4. CodeBuild then executes the commands specified in the buildspec.yml file within this temporary directory.
   The code, dependencies, and build artifacts all reside in this temporary directory during the build process.

### Artifacts Storage:

5. After the build process is complete, the build artifacts (e.g., compiled Angular application) are moved to a designated output directory, as specified in the buildspec.yml file.

   These artifacts are then collected and stored by CodePipeline in the configured S3 bucket or other storage services, depending on your pipeline configuration.

### buildspec.yml

        version: 0.2

        phases:
        install:
            runtime-versions:
            nodejs: 14
            commands:
            - echo Installing dependencies...
            - npm install

        pre_build:
            commands:
            - echo Running pre-build scripts...
            - npm run lint

        build:
            commands:
            - echo Building the Angular project...
            - npm run build -- --prod

        post_build:
            commands:
            - echo Build completed.
            - echo Listing files in the build directory:
            - ls -la dist/your-angular-app

        artifacts:
        files:
            - '**/*'
        base-directory: dist/your-angular-app

        cache:
        paths:
            - node_modules/**/*

### Explanation

#### Install Phase:

#### Set Up Environment:

1. CodeBuild prepares the build environment with the specified runtime versions (e.g., Node.js).
2. Install Dependencies:

   1. The npm install command is executed.
   2. Reads package.json:

      1. CodeBuild uses the package.json file to determine which packages need to be installed.

3. Install Packages:
   1. Dependencies listed in package.json are downloaded from the npm registry.
   2. Installed packages are placed in the node_modules directory.
4. Verify Installation:

   You can use commands like npm list to verify that dependencies are installed correctly.

#### Pre-Build Phase:

1. Purpose: Run preliminary tasks like linting.
2. Command: npm run lint

#### Build Phase:

1. Purpose: Build the application.
   Command: npm run build -- --prod
   Process: Compiles the project and places output in the dist directory.

#### Post-Build Phase:

1. Purpose: Final tasks after build.
   Commands: Optional, such as listing files or additional cleanup.

#### Artifacts Collection:

1. Purpose: Specify which files to keep.
   Configuration: Collects files from the dist directory to be used in deployment.
