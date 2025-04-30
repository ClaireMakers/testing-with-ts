# Setting-up test environments: 

## Setting-up the back-end: 

On your back-end, you want to run two separate environment - your regular development environment, with its associated database, and the test environment, also with its associated database. Keeping two databases aprt from each other allows you to be efficient when doing both manual and automatic testing. 

You will be using the `dotenv` library to switch between the two. 

Your first step should be to create two separate files - .env and .env.test: 

.env
```
MONGODB_URL="mongodb://0.0.0.0/makers_bnb_ts"
```

.env.test
```
MONGODB_URL="mongodb://0.0.0.0/makers_bnb_ts_test"
```

This will set-up two distinct databases. Then, you will need to set up your test environment so it uses the database from the `.env.test` file: 

1. Install the relevant npm libraries:

```
    npm install @types/dotenv supertest vitest
```

2. Create a `vitest.config.ts` file at the same folder level than your `package.json` and paste the following: 

```
import dotenv from "dotenv";

// Load test environment variables
dotenv.config({ path: "./.env.test" });

import { defineConfig } from 'vitest/config';

export default defineConfig({
  test: {
    setupFiles: ['./src/tests/mongoDb_helper.ts'], //Make sure the path to the mongoDb_helper.ts file is correct
    environment: 'node',  // Ensure Node.js environment
    globals: true,        // Use global test functions like `describe` and `it`
  },
});
```

3. Create a `mongoDb_helper.ts` file in your `/tests` folder and add the following to it: 

```
import mongoose from "mongoose";
import  connectToDatabase from "../db/dbConnection.js"
import { beforeAll, afterAll } from "vitest";

beforeAll(async () => {
  await connectToDatabase();
});

afterAll(async () => {
  await mongoose.connection.close(true);
});
```

And that's it for the back-end set-up! You can now run: 

```
npx vitest
```

And it will automatically find and run any test files ending in .test.ts in your source folder :) 

## Setting-up the front-end: 

On your front-end, you will be using a few libraries to get your environment set-up for testing. The main ones will be: 

```
npm install @testing-library/jest-dom @testing-library/react jsdom vitest
```

Just like on the back-end, you will need to set-up your test environment. Create a `vitest.config.ts` file in your folder and copy-paste the following inside: 

```
import { defineConfig } from "vitest/config";

export default defineConfig({
    test: {
        globals: true,
        environment: 'jsdom', // or 'happy-dom' if preferred
        coverage: {
            reporter: ['text', 'json', 'html'],
        },
        setupFiles: './tests/setupTests'
    }
});
```

Then, create a tests folder and create a file `setupTests.ts` with the following inside it: 
```
import '@testing-library/jest-dom'
```

After this, you should be good to go! Test files using this set-up should end in `.test.tsx`, not just `test.ts`, as we are testing a front-end application that features JSX code. 
