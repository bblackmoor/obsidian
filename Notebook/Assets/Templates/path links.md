<%* 
// prompt for the title (thank you https://www.reddit.com/user/JorgeGodoy/)
let title = tp.file.title

if (title.startsWith("Untitled")) {
	title = await tp.system.prompt("Title")
}

await tp.file.rename(title)

let pageTitle = title
let pageTitleHTML = pageTitle.replace(/ /g, "%20")

let day = ""
let dayName = ""
let month = ""
let monthName = ""
let year = ""
let pageContent = ""

// # ADD PATH CHECKING
// # BEGIN JOURNAL SECTION
// Add initial page content
// very simple YYYY-MM-DD date format regex
const datepattern = /^\d{4}-\d{2}-\d{2}$/

if (datepattern.test(pageTitle)) {
	// date pattern, therefore journal entry
	day = tp.date.now("DD", 0, tp.file.title, "YYYY-MM-DD")
	dayName = tp.date.now("dddd", 0, tp.file.title, "YYYY-MM-DD")
	month = tp.date.now("MM", 0, tp.file.title, "YYYY-MM-DD")
	monthName = tp.date.now("MMMM", 0, tp.file.title, "YYYY-MM-DD")
	year = tp.date.now("YYYY", 0, tp.file.title, "YYYY-MM-DD")

	dayName = " " + dayName

	pageContent += "**Went to bed:** \n"
	pageContent += "**Got up:** \n"
	pageContent += "**Weight:** \n"
}
// # END JOURNAL SECTION


// HEADING BREADCRUMBS
const links = []

// get TAbstractFile for current note
const notePath = tp.file.path(true)
let tFile = this.app.vault.getAbstractFileByPath(notePath)

let tFileParentName = ""
let tFileParentNameHTML = ""

let i = 0
let j = 0
let linkParentPath = "../"
let linkPath = ""

while (!tFile.parent?.isRoot() ) {
	i++
	
	tFileParentName = tFile.parent.name
	tFileParentNameHTML = tFileParentName.replace(/ /g, "\%20")

	if (i == 1 && pageTitle == tFileParentName ) {
		pageContent += "%% Waypoint %%\n"
	}

	if (tFile.parent?.isRoot() && i > 2) {
		// this space intentionally left blank
	} else {
		if (i != 1 || pageTitle != tFileParentName ) {
			if (tFile.parent?.parent?.isRoot() && i > 2) {
				// this space intentionally left blank
			} else {
				j = i - 1
				linkPath = ""

				while (j > 0) {
					j--
					linkPath = linkPath + linkParentPath
				}

				// Creating markdown links
				links.unshift("["+ tFileParentName + "](" + linkPath + tFileParentNameHTML + ".md)")
			}
		}
	}

	tFile = tFile.parent
}

if (links.length > 0) {
	tR += links.join(" | ")
	tR += "\n"
}
%># [<% pageTitle %>](<% pageTitleHTML %>.md) <% dayName %>

<% pageContent %><% tp.file.cursor() %><%* app.workspace.activeLeaf.view.editor?.focus(); %>
