# Usage instructions

## Download
- Download [our achievement analyser skill](/game-achievement-analyzer.skill)
- Download and install [Claude for Desktop](https://claude.com/product/overview)
- Launch Claude

## Setup
### Filesystem installation
- Click "Plugins" in the top-right
- Type "Filesystem" into the search box
- Install Filesystem connector
- Enable it, and add your Steam library directory to allowed list 
  - *(The full path is usually `C:\Program Files (x86)\Steam\steamapps\common\`)*
- File > Exit and restart Claude

### Settings configuration
- Capabilities > Create and edit files | ON
- Capabilities > Use the internet while making files | ON
- Capabilities > Allowed websites | STANDARD DOWNLOAD SITES ONLY
- (Optional) Your Claude > Generate memory from chats | ON
  - *The skill can personalise guides based on your own Steam achievement unlocks. It stores whether or not you wish for it to do this in local memory. We recommend having it turned off (and just using the pre-packaged generic Steam check) in most cases.*
 
### Skill installation (this will need to be done for each new skill update)
- Download our custom skill from the link above
- Click "Plugins"
- Click "Add" > "Upload Plugin"
- Drag the `.skill` file into the upload box, and then click "Upload"
  - *(Optional) If your Claude has proposed updates to the skill in previous sessions, you may wish to give it the new .skill in a new chat session and say "Merge this with your existing achievement analyser skill", to avoid losing any changes you've made. Otherwise, you can safely Replace the existing skill*
	
## Generic workflow
- **Always run this skill in Manual mode.** This can be changed at the bottom-right of any chat session. Auto mode is less capable than Manual mode and will be blocked by Oversight from doing many tasks we require. Manual mode will instead request your permission on such tasks instead of outright blocking it.
- Create a new chat session
  - **For Claude Pro**
    - Say "Use achievement analysis skill on this game: C:/path/to/game"
  - **For Claude Free**
    - Attach game's important files (scripts.rpa/rpyc/rpy, Assembly-CSharp.dll, etc) to chat and say "Use your achievement analysis skill on this game. Other files exist at C:/path/to/game - ask for me to upload any that would be useful"
- Select "Allow" when it prompts for folder access
- Wait for completion. This will typically take on the order of 3-5 minutes, but may take much longer for very complex games. The AI may ask you to attach more files, use a tool to split a file up for it to stage, or other administrative tasks. Follow its instructions in these cases.
- If Claude suggests updates to the achievement analyser skill, you can click the "Update plugin" button if its proposed updates seem likely to be useful on future games
    - *(Optional) You can download the updated .skill and send it to Sellyme for these improvements to be added to the base skill.*

# Details
This skill package consists of four main components, each of which packages an extensive array of tools and documentation to make the AI capable of performing the tasks as efficiently as possible. By offloading all of the reasoning, tool creation, and difficult engine analysis to pre-run compute sessions, Free plans can use the results to effectively decompile and analyse games without being constantly throttled by usage limits.

## Engine identification and fingerprinting
Given the list of files available in your install directory, use common patterns and engine fingerprints to identify what tools and resources we should load for analysis of this game. As of v85, the following engines have been thoroughly tested and have explicit tooling support:

- Unity
  - Covers both `Mono` and `il2cpp` compilers
  - Includes the Utage VN framework
- Unreal Engine 
  - Covers both UE4 and UE5.
  - AES-encrypted `.pak` files supported
- Godot
- GameMaker
  - Covers both GM VM and the `YYC` compiler
- Ren'Py
  - supports `.rpy`, `.rpyc`, and `.rpa` file storage
- ChoiceScript
- HTML5
- Electron
- NW.js

Several other engines have some support but have not been tested across a wide range of games:

- HuneX
- Twine
- Tauri
- TyranoScript
- Kirikiri
- Java
- Pygame
- LÖVE
- Flash
- Artemis
- Wolf RPG Editor

Unity specifically has several different Visual Novel frameworks, with varying degrees of popularity and coverage:
- Naninovel
- Utage
- MinusNGIN

## Achievement trigger detection
The skill will look through all of the calls to various Steam API functions to identify any parts of the game code that interact with achievements. It will then analyse the call chain of these functions, and identify any triggers, checks, or requirements that must be met in order for the achievement to be earned.

As part of this stage, it will use the inbuilt web browser to request the achievement metadata from Steam's servers, in order to correlate achievement API names as used in the game code with the display names shown on Steam. This step is optional, and if it fails or you block it the output will still succeed, just with API names instead of human-friendly ones.

Once all achievements have been identified and conflated with Steam data, the AI will generate an `Achievements.md` file, listing every achievement's requirements and status. If broken or buggy achievements are present, they will be mentioned prominently in this file.

## Shortcut detection
The next step is to look for any cheat codes, dev menus, cheesy strategies, logic errors, or other ways to unlock achievements that might not be expected just from their descriptions. This typically comes in the form of hidden consoles, auto-win buttons, or glitches that allow faster progression than intended.

The skill explicitly excludes things that would be in violation of achievement tracker rules (e.g., direct unlock console commands, save editing, file editing), but it is not an authoritative source on tracker rules, so please ask community moderators on anything that you feel unsure about.

If substantial shortcuts are found, the skill will output a `Shortcuts.md` file documenting them.

## Guide
Many games can be trivialised by the creation of a step-by-step guide. If no shortcuts are found (or the ones that are found have limited use), and the game seems like a good candidate for an explicit guide, the skill will work to create a route, verify its functionality through simulation, and then create a `Guide.md` file with the final output.

Guide output will always assume that you are playing in English and present choices or labels in English if the game has explicit English language support - if the game is only available in another language, it will instead present any game text in that language.

Most games require substantial mechanical input or are too broad and open to effectively guide in an explicit step-by-step manner, and the skill will not attempt to do so if this appears to be the case. These are the genres of game it will usually attempt to guide:

- Visual Novels, Dating Sims, and Choose Your Own Adventures
- Hidden Object Games
- Point & Click games
- Puzzle games
- Programming games/Zachlikes
- Linear RPGs
