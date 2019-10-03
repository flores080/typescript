# How To Setup Rollup Config

https://dev.to/proticm/how-to-setup-rollup-config-45mk

#### Installing Rollup

To install rollup and save it as development dependency we should run the following command:

```bash
npm install rollup --save-dev
```



#### Update the `package.json` 

file located in our application root folder.

```json
"devDependencies": {
    "rollup": "^1.10.0" 
}
```



#### Next, create  `rollup.config.js` 

in the application root folder. Inside, add the following.

```json
export default {
    input: './src/main.js',
    output: {
        file: './build/bundle.min.js',
        format: 'iife',
        name: 'bundle'
    }
}
```

Open the `package.json` file and add the following script under the script setting:

```json
"scripts": {
    "build": "rollup -c"
}
```

and run 

```bash
npm run build
```