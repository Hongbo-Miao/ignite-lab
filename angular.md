# Build an Add-in with Angular

An Office Add-in includes two parts, the web app and a manifest file.

### Step 1. Generate the Angular project by **Angular CLI**

Open Visual Studio Code, Click `View` -> `Integrated Terminal`.

In your terminal, input below code and press enter to go to Desktop folder.

```bash
cd Desktop
```

Generate a new Angular app named 'my-addin' by running the following command:

```bash
ng new my-addin
```

### Step 2. Generate the manifest file by **[Yo Office](https://github.com/OfficeDev/generator-office)**

Go to your app folder.

```bash
cd my-addin
```

Use the following command to create the Office manifest file with [the Yeoman generator for Office Add-ins](https://github.com/OfficeDev/generator-office):

```bash
yo office --skip-install
```

When prompted, supply the following information:

|Prompt|Response|
|---|---|
|New subfolder|Press enter or type 'n' to use current directory|
|Add-in name|Press enter to use the default name|
|Supported Office host|Press Enter to choose Excel|
|Create new add-in|No, I only need a manifest file|

![Generate](./img/office-toolbox-generate.png)

> If prompted to overwrite package.json, type 'n' then press Enter to decline.


### Step 3. Initialize

Type the following command into the terminal.

`code .` 

This will open your project in Visual Studio Code. Open the manifest and replace all `https://localhost:3000` to `http://localhost:4200`. The manifest filename ends with **manifest.xml** and is located in the root directory of your project.

Open **src/index.html**, and add the following before the `</head>` tag.

```html
<script src="https://appsforoffice.microsoft.com/lib/1/hosted/office.debug.js"></script>
```

Open **src/main.ts**, and replace

```typescript
platformBrowserDynamic().bootstrapModule(AppModule)
  .catch(err => console.log(err));
```

with the following:

```typescript
declare const Office: any;

Office.initialize = () => {
  platformBrowserDynamic().bootstrapModule(AppModule)
    .catch(err => console.log(err));
};
```

On Windows, since the add-in platform uses Internet Explorer, uncomment these lines in **src/polyfills.ts**.

```typescript
import 'core-js/es6/symbol';
import 'core-js/es6/object';
import 'core-js/es6/function';
import 'core-js/es6/parse-int';
import 'core-js/es6/parse-float';
import 'core-js/es6/number';
import 'core-js/es6/math';
import 'core-js/es6/string';
import 'core-js/es6/date';
import 'core-js/es6/array';
import 'core-js/es6/regexp';
import 'core-js/es6/map';
import 'core-js/es6/weak-map';
import 'core-js/es6/set';
```

### Step 4. Add "Color Me" component

Open **src/app/app.component.html**. Replace contents with:

```html
<button (click)="onColorMe()">Color Me</button>
```

Open **src/app/app.component.ts**. Replace contents with:

```typescript
import { Component } from '@angular/core';

declare const Excel: any;

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  onColorMe() {
    Excel.run(async (context) => {
      const range = context.workbook.getSelectedRange();
      range.format.fill.color = 'blue';
      await context.sync();
    });
  }
}
```

### Step 5. Run the app

Save all changes in VS Code. Reopen the terminal. Make sure you are in the root directory of the project, then run the dev server:

```bash
npm start
```

### Step 6. Side load the manifest file into Office

To run the add-in, you need load the add-in into Excel. Below, we are using an open-source project currently in development, called [Office-Toolbox](https://github.com/OfficeDev/office-toolbox). It is not part of the official Office toolchain yet, but makes the sideloading process easier. Try office-toolbox below! Or you can follow our manual sideloading process documented [here](https://dev.office.com/docs/add-ins/testing/create-a-network-shared-folder-catalog-for-task-pane-and-content-add-ins).

#### Run Office-Toolbox

To use office-toolbox, open a new terminal window, and navigate to the root directory of the project (c:\Users\Administrator\Desktop\My-Office-Addin). Run the following command.

```bash
office-toolbox sideload -m my-office-add-in-manifest.xml -a excel
```

> **Don't Panic!** Office-Toolbox may spit out some errors, but it will still load your add-in into Excel.

Office-Toolbox will then launch Excel with your add-in loaded. Click the 'Show Taskpane' button on the 'Home' tab to reveal the taskpane.

Select the range and click **Color Me** button.

![Result](./img/result.png)

#### Congratulations! You just finished your first Angular add-in for Excel! 

> **Did You Know:** You can also run 'office-toolbox' without passing in arguments, and you will be prompted as shown in the image below.
![Sideload](./img/office-toolbox-sideload.png)
