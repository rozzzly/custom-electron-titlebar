# Custom Electron Titlebar

This project is a typescript library for electron that allows you to configure a fully customizable title bar.

##  ⚠️🚨 **[ NOTE ]** 🛑🚧
The project was ~~abandoned~~ _archived_ by its original author. Just prior to being archived, [#154](https://github.com/AlexTorresSk/custom-electron-titlebar/pull/154) had been merged which makes use of [`@electron/remote`](https://github.com/electron/remote) instead of the deprecated `electron.remote` that was removed in electron v14. To be frank, I just wanted support for v14. That's why I forked and will be publishing to npm under the `@rozzzly` scope. I don't really have any intention of adding new features. If some bug is affecting me, I'll patch and release. But can't promise that I'll have much time to fixing any _reported_ bugs. **If anyone PR's a bugfix or new feature, I'll try to merge them and publish promptly.** Otherwise, 🤷‍♂️ _"when I get around to it."_

## It is a library for **electron**, it **cannot** be used on a **normal website**.

[![LICENSE](https://img.shields.io/github/license/AlexTorresSk/custom-electron-titlebar.svg)](https://github.com/AlexTorresSk/custom-electron-titlebar/blob/master/LICENSE)
[![NPM Version](https://img.shields.io/npm/v/custom-electron-titlebar.svg)](https://npmjs.org/package/custom-electron-titlebar)

![Preview 1](screenshots/window_1.png)

![Preview 2](screenshots/window_2.png)

![Preview 3](screenshots/window_3.png)

## Install

```
yarn add @rozzzly/custom-electron-titlebar
```
or if you prefer the more vanilla npm:
```
npm i @rozzzly/custom-electron-titlebar
```

## Usage

#### Step 1
In your app's **renderer** entrypoint or in an **HTML script tag** add:


```ts
import { Titlebar, Color } from 'custom-electron-titlebar'

new Titlebar({
	backgroundColor: Color.fromHex('#ECECEC')
});
```

The parameter `backgroundColor: Color` is required, this should be `Color` type.
(View [Update Background](#update-background) for more details).


#### Step 2

Import `initialize` from `@electron/remote/main` then call it _(ie: `initialize()`)_ somewhere near the top of your app's **main** entrypoint/

```ts
/* ...other imports... */
import { initialize } from '@electron/remote/main';
/* ...other imports... */

initialize(); // invoke @electron/remote/main.initialize

// the rest of your app's main entrypoint
app.on('ready', () => {
	// ...
});
```



#### Step 3
Update the code that launches browser window
```ts
mainWindow = new BrowserWindow({
    width: 1000,
    height: 600,
    titleBarStyle: 'hidden', // add this line
});
```

## ⚠️🚨 **[ Warning ]** 🛑🚧
Until [#72](https://github.com/electron/remote/pull/72) gets merged in `@electron/remote`and published, you'll still need to specify
`webPreferences: { enableRemoteModule: true }` when creating a new `BrowserWindow`. **In `electron` v14 that option has been "removed" from the docs and typescript definitions but adding the `enableRemoteModule` still works.**

If you're a typescript user, try the following little hack:
```ts
mainWindow = new BrowserWindow({
    width: 1000,
    height: 600,
    titleBarStyle: 'hidden',
	webPreferences: {
		['enableRemoteModule' as any]: true
	}
});
```

After that gets merged and published, it _**looks like**_ _(although it may change)_ the new API will be the following:

```ts
import { app, BrowserWindow } from 'electron';
import { initialize, permit } from '@electron/remote/main';

initialize();
let mainWindow;

app.on('ready', () => {
	mainWindow = new BrowserWindow({
		width: 1000,
		height: 600,
		titleBarStyle: 'hidden'
	});

 	// permit() must be called on the webContents of each BrowserWindow that will get a custom titlebar
	permit(mainWindow.webContents);
})
```



## Options

The `Titlebar` constructor takes several options:

| Parameter                      | Type             | Description                                                                                                                     | Default                   |
| ------------------------------ | ---------------- | ------------------------------------------------------------------------------------------------------------------------------- | ------------------------- |
| backgroundColor                |  Color            | The background color of the titlebar.                                                                                           | #444444                   |
| icon                           | string           | The icon shown on the left side of the title bar.                                                                               | null                      |
| iconsTheme                     | Theme            | Style of the icons.                                                                                                             | Themebar.win              |
| shadow                         | boolean          | The shadow of the titlebar.                                                                                                     | false                     |
| drag                           | boolean          | Define whether or not you can drag the window by holding the click on the title bar.                                            | true                      |
| minimizable                    | boolean          | Enables or disables the option to minimize the window by clicking on the corresponding button in the title bar.                 | true                      |
| maximizable                    | boolean          | Enables or disables the option to maximize and un-maximize the window by clicking on the corresponding button in the title bar. | true                      |
| closeable                      | boolean          | Enables or disables the option of the close window by clicking on the corresponding button in the title bar.                    | true                      |
| order                          | string           | Set the order of the elements on the title bar. (`inverted`, `first-buttons`)                                                   | null                      |
| titleHorizontalAlignment       | string           | Set horizontal alignment of the window title. (`left`, `center`, `right`)                                                       | center                    |
| menu                           | Electron.Menu    | The menu to show in the title bar.                                                                                              | Menu.getApplicationMenu() |
| menuPosition                   | string           | The position of menubar on titlebar.                                                                                            | left 					  |
| enableMnemonics                | boolean 		    | Enable the mnemonics on menubar and menu items.																		          | true                      |
| itemBackgroundColor            | Color            | The background color when the mouse is over the item.                                                                           | rgba(0, 0, 0, .14)        |
| hideWhenClickingClose          | boolean          | When the close button is clicked, the window is hidden instead of closed.                                                       | false                     |
| overflow                       | string           | The overflow of the container (`auto`, `visible`, `hidden`)                                                                     | auto                      |
| unfocusEffect                  | boolean          | Enables or disables the blur option in the title bar.                                                                           | false                     |

## Methods

### Update Background

This change the color of titlebar and it's checked whether the color is light or dark, so that the color of the icons adapts to the background of the title bar.

```js
titlebar.updateBackground(new Color(new RGBA(0, 0, 0, .7)));
```

To assign colors you can use the following options: `Color.fromHex()`, `new Color(new RGBA(r, g, b, a))`, `new Color(new HSLA(h, s, l, a))`, `new Color(new HSVA(h, s, v, a))` or `Color.BLUE`, `Color.RED`, etc.

### Update Items Background Color

This method change background color on hover of items of menubar.

```js
titlebar.updateItemBGColor(new Color(new RGBA(0, 0, 0, .7)));
```

To assign colors you can use the following options: `Color.fromHex()`, `new Color(new RGBA(r, g, b, a))`, `new Color(new HSLA(h, s, l, a))`, `new Color(new HSVA(h, s, v, a))` or `Color.BLUE`, `Color.RED`, etc.

### Update Title

This method updated the title of the title bar, If you change the content of the `title` tag, you should call this method for update the title.

```js
document.title = 'My new title';
titlebar.updateTitle();

// Or you can do as follows and avoid writing document.title
titlebar.updateTitle('New Title');
```

if this method is called and the title parameter is added, the title of the document is changed to that of the parameter.

### Update Icon

With this method you can update the icon. This method receives the url of the image _(it is advisable to use transparent image formats)_

```js
titlebar.updateIcon('./images/my-icon.svg');
```

### Update Menu

This method updates or creates the menu, to create the menu use remote.Menu and remote.MenuItem.

```js
const menu = new Menu();
menu.append(new MenuItem({
	label: 'Item 1',
	submenu: [
		{
			label: 'Subitem 1',
			click: () => console.log('Click on subitem 1')
		},
		{
			type: 'separator'
		}
	]
}));

menu.append(new MenuItem({
	label: 'Item 2',
	submenu: [
		{
			label: 'Subitem checkbox',
			type: 'checkbox',
			checked: true
		},
		{
			type: 'separator'
		},
		{
			label: 'Subitem with submenu',
			submenu: [
				{
					label: 'Submenu &item 1',
					accelerator: 'Ctrl+T'
				}
			]
		}
	]
}));

titlebar.updateMenu(menu);
```

### Update Menu Position

You can change the position of the menu bar. `left` and `bottom` are allowed.

```js
titlebar.updateMenuPosition('bottom');
```

### Set Horizontal Alignment

> setHorizontalAlignment method was contributed by [@MairwunNx](https://github.com/MairwunNx) :punch:

`left`, `center` and `right` are allowed

```js
titlebar.setHorizontalAlignment('right');
```

### Dispose

This method removes the title bar completely and all recorded events.

```js
titlebar.dispose();
```

## CSS Classes
The following CSS classes exist and can be used to customize the titlebar

| Class name                  | Description                                     |
| --------------------------- | ----------------------------------------------- |
| .titlebar                   | Styles the titlebar.                            |
| .window-appicon             | Styles the app icon on the titlebar.            |
| .window-title               | Styles the window title. (Example: font-size)   |
| .window-controls-container  | Styles the window controls section.             |
| .resizer top                | Description missing                             |
| .resizer left               | Description missing                             |
| .menubar                    | Description missing                             |
| .menubar-menu-button        | Styles the main menu elements. (Example: color) |
| .menubar-menu-button open   | Description missing                             |
| .menubar-menu-title         | Description missing                             |
| .action-item                | Description missing                             |
| .action-menu-item           | Styles action menu elements. (Example: color)   |

## Contributing

Many thanks to all the people who support this project through issues and pull request.
If you want to contribute with this project, all the issues and pull request are welcome, or we can chat a bit in the [discussions](https://github.com/AlexTorresSk/custom-electron-titlebar/discussions)

You can also:<br>
<a href="https://www.buymeacoffee.com/bjkGN4g" target="_blank"><img src="https://www.buymeacoffee.com/assets/img/custom_images/orange_img.png" alt="Buy Me A Coffee" style="height: 41px !important;width: 174px !important;box-shadow: 0px 3px 2px 0px rgba(190, 190, 190, 0.5) !important;-webkit-box-shadow: 0px 3px 2px 0px rgba(190, 190, 190, 0.5) !important;" ></a>

## License

This project is under the [MIT](https://github.com/AlexTorresSk/custom-electron-titlebar/blob/master/LICENSE) license.
