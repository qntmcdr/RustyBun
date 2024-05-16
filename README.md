<div align="center">
<img src="gui/Clipb.svg" width="140px" />
<div>
  <h1>RustyBun</h1>
</div>

<p>RustyBun is a clipboard management tool designed to streamline your workflow by providing convenient access to copied items. With RustyBun, users can easily access a history of copied text, eliminating the need to switch between multiple applications or re-copy content</p>

![image](https://github.com/RiturajKulshresth/RustyBun/assets/43854753/7c76d552-65fd-4759-bb25-60e59c7c0723)
</div>
## Features
- Simple, easy-to-use, type-safe
- Responsive layout
- Maintains history 
- Prevents over-usage of memory by cleaning after 200 clips
- One-click copy 

## Installation
(Currently only for Debian-based distros)
Use the [.deb file](https://github.com/RiturajKulshresth/RustyBun/releases/tag/1.0.0) and install it on your Debian system.



## Overview 

Inspired by the Windows clipboard, the awesome devs of Rust, and Stranger Things' Dusty Bun, RustyBun aims to bring you ease of use and the ability to have multiple copied texts available to you at one click. The structure consists of three parts: Rust Backend, Electron GUI, and the build folder.

### 1. Rust Backend

Rust Backend is the main logic for taking stuff from the x11-clipboard and saving it in the `clipboard.json` file. I chose to save the copied text in a file as I needed the feature for sudden crashes of devices so that the copied text remains. It also uses serde to handle JSON files.

#### Parts in `main.rs`

- **main:** 
  - The execution starts here. 
  - It checks if the folder `/home/ritu/Documents/RustyBun` is present or not. If not, it creates this folder. 
  - It initializes the clipboard, runs an infinite loop to track clipboard changes, and handles appending data to `clipboard.json`. 
  - It also checks if the created JSON is valid. If it's not, it simply clears the `clipboard.json` and increments the counter.

- **load_id_counter:** 
  - It takes the latest ID of the entry in the JSON. 
  - This is used to keep track of the number of entries done to the clipboard. 
  - When the counter reaches 200, the JSON is purged on the next start of the application, and a fresh clipboard is given.

- **save_id_counter:** 
  - It saves the latest ID in the `id_counter.txt` file. 
  - If the file does not exist, it creates the file and then puts the value. 
  - Else, it removes the data from the file and enters the latest value.

- **append_to_json:** 
  - This is the most important function of the Rust part of the code. 
  - It checks if the clipboard is present in the location `/home/<user>/Documents/RustyBun/clipboard.json` as an absolute path. 
  - If it is not present, it creates the file and puts an empty JSON. 
  - It then opens the file and parses it to convert it to JSON. 
  - It creates a new entry from the value received by it from the main function and parses the new JSON after appending the new value to the old JSON and then clears the file to write the new JSON.

### 2. Electron GUI

- **main:** 
  - It reads the file in the `main.js` itself and sends data to the mainWindow renderer and if there is any change in the file then also sends that data. 
  - It also tries to hide the menu with unnecessary features. 
  - If we try to read the clipboard in renderer if rails to read due to some issue.

- **style.css:** 
  - Handles all the styling details.

- **renderer:** 
  - First, we try to run the binary file that is generated by building the Rust part as a spawn which uses a new thread apparently. 
  - Whenever there is any console changes, it prints the console of the program. 
  - `ipcRenderer` renders the GUI boxes based on the `clipboard.json` data provided. 
  - Whenever the mouse is near the top of the menu, it unhides the menu. 
  - `renderJSON` renders the basic window and the boxes. 
  - Loops through each object in the JSON data in reverse order creates new boxes for the JSON and re-sends them to the DOM forcing it to re-render.

### 3. Build Folder

- **build.sh:**
  - The build folder has the build script so if you want to build the application from the repository you could simply run the `./build.sh`.

 
### Contribution / Feedback
Contributions and feedback are greatly appreciated! Please create a new topic/issue for them.


### Future Work
- Make the path for `clipboard.json` dynamic for each user.
- Handle opening of multiple instances better or altogether stop multiple instances from opening. If multiple instances are opened, the `clipboard.json` file is overwritten by both instances, causing invalid JSON.
- Make sure that the full menu is not visible at startup.
- Make sure that the zoom features work.
- Add an option to start the program at startup.
- Consider removing `id_counter.txt` as it does not serve a lot of purpose (if I can read the latest ID in the JSON).
- Add a preference menu to change the color scheme.
- Add a clear option to the clipboard.
- Add a clear option to each entry/box.
- Error handled in line 51-65 of `backend/src/main.rs` can be handled in code line 153 (`serde_json::from_str(&json_data)`) as the JSON becomes invalid when checked here and Rust panics.
- Give the user the option to set the cleanup limit for clipboard entries.



