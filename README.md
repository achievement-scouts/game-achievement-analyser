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

# Limitations

While this tool is incredibly powerful, there are several limitations in what it can do. Some of these limitations only exist on the Free version of Claude, and others are more general.

## Free vs Paid
Claude comes with four plans for personal use: Free, Pro, Max, and Ultra. Their monthly costs in USD are $0, $20, $100, and $200 respectively. Claude Pro can also be billed at $17/month on a yearly plan.

In each section below we'll look at the differences between the plans in relation to this skill's capabilities and usage. Benefits or differences not relevant to us are not covered.

### Model Quality
Claude currently offers four model varieties: Haiku, Sonnet, Opus, and Fable. These are listed in ascending order of capabilities.

- **Haiku**: Available in all plans. Intended only for very short tasks, like language classification. This model is not suitable for running this skill.
- **Sonnet**: Available in all plans. General-purpose model, but struggles on very complex tasks. Can easily guide a basic VN, identify exactly what an achievement's requirements are, or find any dev modes/cheat codes, but may struggle with accurately guiding very long and intricate games with a lot of stats that need to meet triggers at the same time.
- **Opus**: Available only in paid plans (Pro/Max/Ultra). Opus can fairly reliably complete any gaming-related task you throw at it. It can easily crack encrypted files, write accurate full guides for the most complex dating sims, create solvers for novel puzzle games, make step-by-step pathing for point-and-clicks, and much more. This is the preferred model if you have access to it.
- **Fable**: Available only in high-tier plans (Max/Ultra), and can only be used for 50% of your weekly usage limits. Fable is designed for very long-form tasks, e.g., software development projects that stretch over months. As most game analyses are single-shot prompts that have runtime measured in minutes, this model is substantially stronger than is necessary for our purposes.

**TL;DR:** Sonnet is okay for a lot of games, but Free users may have difficulty generating fully accurate guides for sufficiently complex games. If you have a paid plan, Opus is preferable.

### Quality of Life features
Claude's paid plans offer some functionality that the skill uses by default. We have fallbacks for Free plan users, and none of these functionalities are critical, but it will affect the user workflow.

- **Folder access**: If you give it approval, Claude Pro (and higher) can directly access a game's install directory on your device and automatically pull files into its workspace to analyse them. Claude Free cannot do this: it can *view* the folder, but needs you to manually attach files in order to perform real analysis on them. The skill should detect if you are on the Free plan and give you a list of such files.
- **Browser access**: The skill prefers to guide achievements using the names they're given in the Steam client, but game code typically only contains API names. By default it uses the Claude in-built browser to check this, but this is only available in paid plans. On Claude Free, it will fail and always use API names. This is fine (and sometimes even preferred) as far as Achievement Scouts is concerned, but if you wish to you can work around this in two ways:
	- Providing a link to the game's achievement list in the form `https://steamcommunity.com/id/{steamuser}/stats/{appid}/?xml=1` and telling it to use WebFetch to get achievement names from this link
	- Pasting in a JSON file from the Steam API: `https://api.steampowered.com/ISteamUserStats/GetSchemaForGame/v2/?appid={appid}&key={apikey}&format=json`

### Usage limits

- **5-hour limits**: All Claude plans except for Ultra have a limit to how much processing you can do in a 5 hour window. These limits vary dynamically based on current service activity levels. On the Free plan you can typically get 2 or 3 simple games processed in each 5 hour window, but sufficiently complex games may take multiple 5-hour limits to complete. Below is an estimation of paid plans usage limits, assuming the model used is Opus 5.5.
	- **Pro**: ~8-10 games analysed.
	- **Max**: ~50 games analysed (5x).
	- **Ultra**: No 5-hour limit at all.
- **Weekly limits**: All paid Claude plans have a weekly limit. Claude Free does not technically have a weekly limit (the 5-hour limits and weaker models mean usage is sufficiently limited already), so if you're using Free, maxing out your 5-hour limit as frequently as possible is advised. Below is an estimation of paid plan usage limits, assuming Opus 5.5.
	- **Pro**: ~50 games analysed.
	- **Max**: ~250 games analysed (5x).
	- **Ultra**: ~500 games analysed (10x).

Unless you're doing heavy automation of the analysis process, it's very unlikely that you will be able to fully utilise the Max or Ultra plans, and we do not recommend them. Pro is sufficient for almost all use cases, only extremely heavily active scouts are likely to regularly hit its weekly limits.

## Filesize limitations

Claude can only copy files of less than 400MB in size into its workspace for analysis. This is not a major problem for most games: we don't care about graphical assets, and those account for the vast majority of all files in a game. The actual code and logic we're looking at will never be this large.

However it's very common for games to package all of their code up in the same file as all of their graphical assets, and if Claude is not able to decrypt/decompress these files in-place it may not be able to start the analysis. If this occurs, it should give you a message informing you of the problem, as well as a suggestion for a tool you could use to unpack the file yourself, and then give Claude access to the folder containing that unpacked file (where it can proceed to copy only the small code-related files).

This may require installation of engine-specific command line tools onto your device for performing manual extraction, such as `retok` and `repak` for Unreal Engine games, or `rpatool` for Ren'Py. It should give fairly clear and easy-to-follow instructions for how to operate these tools.

## Reverse engineering limitations

Since 2 November 2026, Claude models have had tighter restrictions on "game cloning". Due to these restrictions, it is sometimes hesitant to crack encryption on files containing game assets. Notably, these limitations are not intended to block the task that we are performing, and as such Claude will only refuse to perform the task if it is framed in a way such that it seems like it might be someone trying to reframe asset ripping as a legitimate activity. The skill has wording to explicitly state its purpose and actions in a way that assuages concern about cracking encrypted files, but if you need to provide follow-up prompts your wording will also matter.

Whenever asking Claude to decrypt a file through non-traditional methods (bytecode analysis, bruteforce, etcetera) always reiterate that this is for the sole purpose of verifying the achievement triggers, that extraction of audio/visual assets is not necessary or desired, and that only the analysis results need to be put in chat, not any of the extracted files.

We have not had any analysis session fail on system-wide guardrail contraints with these instructions.

## Weird quirks and edge cases

Games with an emoji in their install directory path can not be accessed with the Filesystem tool. Their important files must be identified in advance and copied in by hand for Free plan users. Paid plan users can use the "Add folder" option from the "+" icon at the bottom left of the chat window to attach the folder in a way that it can be read and loaded like usual.

