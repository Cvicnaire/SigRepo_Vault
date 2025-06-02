# SigRepo Content Vault

# Obsidian Plugins

We use Obsidian+Git to collaboratively edit this content. Below are some conventions, tips and tricks to help with that.

## Local Obsidian setup
Follow these steps to get a local version of this vault:

1. Install Git if you don't already have it
	1. On Windows, install via the [standalone installer](https://git-scm.com/download/win)
	2. On Mac/Linux, ensure the `git` executable is on your PATH when running from a terminal
2. Optionally, install GitHub Desktop, because it's nice
2. Clone this repo to your local computer in such a way as you can push commits to it
3. Install [Obsidian](https://obsidian.md/)
4. If you have only just installed Obsidian, you should see a splash page asking if you'd like to create a new vault or open an existing one. Click on "Open folder as vault" on the next page, and select the directory where you cloned this repo.
5. Go to Settings/Community Plugins and enable them
6. Click Browse and install the following community plugins (make sure to enable them after they are installed):
	1. [Git](obsidian://show-plugin?id=obsidian-git)
	2. [Dataview](obsidian://show-plugin?id=dataview)
	3. [Tasks](obsidian://show-plugin?id=obsidian-tasks-plugin)
	4. [Advanced Slides](obsidian://show-plugin?id=obsidian-advanced-slides)
	5. [Advanced Tables](obsidian://show-plugin?id=table-editor-obsidian)
	6. [Excalidraw](obsidian://show-plugin?id=obsidian-excalidraw-plugin)
	7. [Homepage](obsidian://show-plugin?id=homepage)
7. Change the default location where attachments are stored by clicking on settings: 
   - **Settings > Files and links **
	   - **Default location for new attachments** = In subfolder under current folder
	   - **Subfolder name** = attachments
1. Configure the Git extension to auto commit/push and pull every 15 minutes:
   - **Settings > Community plugins > Git**
	   - **Vault backup interval (minutes)** = 15
	   - **Auto-pull interval** = 15
2. Configure Excalidraw to auto export SVG and PNG. This will enable our Excalidraw images to be embedded into Advanced Slides presentations. The settings are deeply nested: 
   - **Excalidraw > Embedding Excalidraw into your Notes and Exporting > Export Settings > Auto-export Settings**
	   - **Auto-export SVG** = true
	   - **Auto-export PNG** = true

