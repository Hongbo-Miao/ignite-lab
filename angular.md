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
yo office
```

When prompted, supply the following information:

|Prompt|Response|
|-|-|
|New subfolder|No is default. Press enter or type 'n' to use current directory|
|Add-in name|Type the add-in name used above, _my-addin_|
|Supported Office host|Excel|
|Create new add-in|No, I only need a manifest file|

![Generate](./img/office-toolbox-generate.png)

> If prompted to overwrite package.json, type 'n' to decline.

The manifest filename ends with **manifest.xml** and is located in the root directory of your project.

Open the manifest and replace all the ports in the generated manifest file from `:3000` to `:4200`.

### Step 3. Prepare Angular project to integrate with Office

Open **src/index.html**, and add the following before the `</head>` tag.

```html
<script src="https://appsforoffice.microsoft.com/lib/beta/hosted/office.debug.js"></script>
```


Open **src/main.ts**, and replace `platformBrowserDynamic().bootstrapModule(AppModule);` with the following:

```typescript
declare const Office: any;

Office.initialize = () => {
  platformBrowserDynamic().bootstrapModule(AppModule);
};
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
      range.format.fill.color = 'green';
      await context.sync();
    });
  }
}
```

### Step 5. Run the app

Run the dev server through the terminal.

```bash
npm start
```

### Step 6. Side load the manifest file into Office

To run the add-in, you need load the add-in into Excel. Below, we are using an open-source project currently in development, called [Office-Toolbox](https://github.com/OfficeDev/office-toolbox). It is not part of the official Office toolchain yet, but makes the sideloading process easier. Try office-toolbox below! Or you can follow our manual sideloading process documented [here](https://dev.office.com/docs/add-ins/testing/create-a-network-shared-folder-catalog-for-task-pane-and-content-add-ins).

#### Running Office-Toolbox

Open a new terminal, and run the following command. Replace 'my-addin-manifest.xml' with the name of the manifest file in your root directory (if different).

```bash
office-toolbox -m my-addin-manifest.xml -a excel 
```
> **Did You Know:** You can also run 'office-toolbox' without passing in arguments, and you will be prompted as shown in the image below.
![Sideload](./img/office-toolbox-sideload.png)

Your add-in should open in Excel. Click the 'Show Taskpane' button on the 'Home' tab to open your taskpane!

![Final Result]()

#### Congratulations! You just finished your first Angular add-in for Excel! 

