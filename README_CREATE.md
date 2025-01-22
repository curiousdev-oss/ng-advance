1. Create the basic angular application I am using angular v18
2. Install the lint using $ ng lint command
3. lint will ask to install @angular-eslint/schematics, say yes and it will install required packages and create a eslint.config.js file with default configuration which already included the recommended angular + typescript practices, also it will add ng lint in the package.json as well to run the lint
   - $ ng lint
   - $ ng lint --fix

# eslint.config.js

// @ts-check
const eslint = require("@eslint/js");
const tseslint = require("typescript-eslint");
const angular = require("angular-eslint");

module.exports = tseslint.config(
{
files: ["**/*.ts"],
extends: [
eslint.configs.recommended,
...tseslint.configs.recommended,
...tseslint.configs.stylistic,
...angular.configs.tsRecommended,
],
processor: angular.processInlineTemplates,
rules: {
"@angular-eslint/directive-selector": [
"error",
{
type: "attribute",
prefix: "app",
style: "camelCase",
},
],
"@angular-eslint/component-selector": [
"error",
{
type: "element",
prefix: "app",
style: "kebab-case",
},
],
},
},
{
files: ["**/*.html"],
extends: [
...angular.configs.templateRecommended,
...angular.configs.templateAccessibility,
],
rules: {},
}
);

4.  npm install prettier -D

    https://dev.to/xansiety/angular-14-prettier-husky-setup-4hcm/#:~:text=Angular%2014%20%2B%20Prettier%20%2B%20Husky%20Setup%201,...%207%207.Testing%20Prettier%20%2B%20ESLint%20%2B%20Husky

    .prettierrc {
    "printWidth": 100,
    "tabWidth": 2,
    "tabs": false,
    "singleQuote": true,
    "semicolon": true,
    "quoteProps": "preserve",
    "bracketSpacing": true
    }
    .prettierignore
    package.json
    package-lock.json
    yarn.lock
    node_modules
    dist
    coverage

5.  npm install pretty-quick -D
    "pretty-quick": "pretty-quick --staged",

6.  npx husky-init && npm install
    6.1 .husky.commit-msg
    #!/usr/bin/env sh
    . "$(dirname -- "$0")/\_/husky.sh"

            npm run lint

    6.2 .husky.pre-commit
    #!/usr/bin/env sh
    . "$(dirname -- "$0")/\_/husky.sh"

            npm run pretty-quick

    6.3 .husky.pre-push
    #!/usr/bin/env sh
    . "$(dirname -- "$0")/\_/husky.sh"

            npm run test

7.  npm i -D jest jest-preset-angular @types/jest

    https://medium.com/@philip.mutua/setting-up-jest-in-your-angular-16-project-3638ef65f3a3

    "test": "jest",
    "test:watch": "jest --watch",
    "test:coverage": "jest --coverage",

    - remove all the other karma and jasmine related files and commands on the .json packages

    - npm uninstall @types/jasmine jasmine-core karma karma-chrome-launcher karma-coverage karma-jasmine karma-jasmine-html-reporter

    - jest.config.js in root

                globalThis.ngJest = {
                skipNgcc: true,
                tsconfig: 'tsconfig.spec.json', // this is the project root tsconfig
                };

                /** @type {import('@jest/types').Config.InitialOptions} */
                module.exports = {
                preset: 'jest-preset-angular',
                testEnvironment: 'jsdom',
                setupFilesAfterEnv: ['<rootDir>/src/setup-jest.ts'],
                transform: {
                    '^.+\\.ts$': 'ts-jest', // Only transform .ts files
                },
                transformIgnorePatterns: [
                    '/node_modules/(?!flat)/', // Exclude modules except 'flat' from transformation
                ],
                moduleDirectories: ['node_modules', 'src'],
                fakeTimers: {
                    enableGlobally: true,
                },
                };

    - setup-jest.ts in src
      import { setupZoneTestEnv } from 'jest-preset-angular/setup-env/zone';
      setupZoneTestEnv();

All done now

Result When we try to commit the code on the github the below things will happen

1. pretty-quick will run and format the code and check for the lint.
2. If lint succeed, commit also succeed else need to fix the lint problem.
3. on code push to the github, all the jest test cases will run, if all the test cases passes then only the code will be pushed else need to fix the test.
