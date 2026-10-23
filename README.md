# Classroom Manager

A faster, cleaner front end for Google Classroom. Post and schedule work in one form, publish drafts in bulk, and copy a whole course's structure out to a Google Sheet and back into a new course.

**[Open Classroom Manager →](https://ironstamp.github.io/classroom-tool)**

It's a single HTML page that talks directly to Google's APIs from your browser. There's no server and no database: nothing is stored anywhere except your own Google account and your browser.

---

## What it does

| Tab | What it's for |
|---|---|
| ⚡ **Quick Post** | Create an assignment, material or announcement in one form, and post it as a draft, scheduled or published straight away |
| 📋 **Manage Drafts** | See every draft in a class, give them dates and times, and publish several at once |
| ⬇️ **Download Course** | Export a course's topics, assignments, materials and announcements to a Google Sheet |
| ⬆️ **Upload Course** | Build a new course (or add to an existing one) from that Sheet, or from a folder of files made by `classroom_archive.py` |
| ⚙️ **Settings** | Client ID, period times, theme |

### Quick Post

- **Type:** Assignment, Material or Announcement. The form only shows the fields that type needs.
- **Class and Topic:** topics load automatically when you pick a class.
- **Assign to:** all students, or pick individual students from the class roster.
- **Scheduling:** pick a date, then tap a period chip (P1–P6) to set the post time. There's a separate set of chips for Wednesday's timetable, and the right set is chosen automatically for the date.
- **Due date and time:** time defaults to 11:59 pm. Untick *Close submissions after due date* if you want late work.
- **Points, grade category and submission type** (Any, Google Doc, or No submission).
- **Materials:** attach files from your Drive (My Drive, Starred, Shared drives, Recent), add a link, or upload a file from your computer.

### Manage Drafts

Pick a class to list its drafts. Select one or more, set a scheduled date/time (and optionally a due date), then either **Save as Draft** or **Schedule & Publish**. **Publish Selected** pushes the selected drafts out immediately.

### Download Course → Upload Course

This is how to roll a course over to a new year, or rebuild it under another account.

1. **Download Course:** choose a course (active or archived), tick what to include, and click **Download to Sheets**. A new Google Sheet appears in your Drive. **Empty Template** gives you just the topic list.
2. Edit the Sheet if you like: rename topics, delete rows, add new ones.
3. **Upload Course:** paste the Sheet's URL, click **Preview**, check it, then **Create Course** (or switch to **Add to existing course**).

By default everything is uploaded as a **draft with no due dates**, so nothing reaches students until you publish it. Tick *Keep original status and due dates* to copy those across too.

Student rosters, submissions and grades are never exported.

#### Sheet format

Row 1 is the header. Each following row is one item, and `TOPIC` rows group the items beneath them.

| Column | Contents |
|---|---|
| `TYPE` | `COURSE`, `TOPIC`, `ASSIGNMENT`, `MATERIAL` or `ANNOUNCEMENT` |
| `TOPIC` | Topic name (filled in for assignments and materials) |
| `TITLE` | Title (for `TOPIC` rows, the topic's name; for announcements, the text) |
| `DESCRIPTION` | Instructions or description |
| `DUE_DATE` | `YYYY-MM-DD` |
| `DUE_TIME` | `HH:MM` (24-hour) |
| `MAX_POINTS` | Number, or blank for ungraded |
| `LINKS` | Comma-separated URLs (Drive files, web links, YouTube, Forms) |
| `STATE` | `DRAFT`, `PUBLISHED` |
| `SCHEDULED_DATE` | `YYYY-MM-DD` |
| `SCHEDULED_TIME` | `HH:MM` |

#### Uploading from an archive folder

If you've used `classroom_archive.py` (from the drive-backup scripts) to download a course's attachments to your computer, choose **Archive folder** as the source and pick the folder containing `course_local.csv`. The files are uploaded to a new folder in your Drive and attached to the right posts. Word, Excel and PowerPoint files can be converted to Google Docs, Sheets and Slides on the way in.

---

## Getting started

Open the [live page](https://ironstamp.github.io/classroom-tool), click **Sign in with Google**, and sign in with the account that owns your Classroom courses.

> **"Access blocked" or "This app isn't verified"?** The tool uses a Google Cloud project that's in *Testing* mode, so only accounts added as test users can sign in. If you're not on the list, set up your own Cloud project (below). It's free and takes about 15 minutes.

### Using your own Google Cloud project

You'll need this if you're not an approved test user, or if you're running the tool at a new school.

1. Go to [console.cloud.google.com](https://console.cloud.google.com) and create a project.
2. **APIs & Services → Library:** enable the **Google Classroom API**, **Google Sheets API** and **Google Drive API**.
3. **APIs & Services → OAuth consent screen:** choose *External* (or *Internal* if your school's Workspace allows it), fill in the app name and your email, and add yourself under **Test users**.
4. **APIs & Services → Credentials → Create credentials → OAuth client ID:**
   - Application type: **Web application**
   - Authorised JavaScript origins: the address you'll serve the page from, e.g. `https://<your-username>.github.io` and `http://localhost:8000` for local testing
5. Copy the client ID (it ends in `.apps.googleusercontent.com`).
6. In `index.html`, find this line near the top of the script and replace the ID with yours:

   ```js
   const HARDCODED_CLIENT_ID = '…apps.googleusercontent.com';
   ```

> The **OAuth Client ID** field in Settings doesn't currently stick: the hard-coded ID is restored every time the page loads. Edit `index.html` as above instead.

If Google warns that the app is unverified when you sign in, click **Advanced → Go to … (unsafe)**. It's your own app, so that's expected.

### Permissions it asks for

| Access | Why |
|---|---|
| Classroom courses, topics, coursework, materials, announcements | Reading and creating posts and courses |
| Classroom rosters (read-only) | Listing students for *Assign to* |
| Google Sheets | Writing the Download sheet and reading the Upload sheet |
| Drive (read-only, plus files it creates) | Browsing Drive for attachments and uploading files |
| Your name and email | Showing who's signed in |

Your sign-in token is held in the page's memory and disappears when you close the tab.

---

## Changing the period times

The P1–P6 chips come from a fixed timetable in `index.html`. To match a different school's bell times, edit:

```js
const TIMETABLE = {
  regular:   ['08:30','09:20','10:30','11:20','13:50','14:40'],
  wednesday: ['08:45','09:35','10:45','11:35','13:50','14:40'],
};
```

The `wednesday` list is used automatically for Wednesdays; `regular` for every other day.

---

## Running it yourself

It's one self-contained file, so any static host works.

- **GitHub Pages:** fork the repo, then Settings → Pages → Deploy from branch → `main`. Add `https://<your-username>.github.io` to your OAuth client's JavaScript origins.
- **Locally:** `python3 -m http.server 8000` in the repo folder, then open `http://localhost:8000`. (Opening the file directly with `file://` won't work, because Google sign-in needs a proper web address.)

## Themes

Click 🎨 in the top bar to switch between Plum & Amber, Greyscale, Ocean, Forest and a high-contrast Accessible theme. Your choice is remembered in the browser.

## Built with

- Vanilla HTML, CSS and JavaScript, no build step
- [Google Identity Services](https://developers.google.com/identity/gsi/web) for sign-in
- Google Classroom, Sheets and Drive REST APIs
- [DM Sans / DM Serif Display](https://fonts.google.com/) from Google Fonts

## More classroom tools

- [Group Allocator](https://ironstamp.github.io/group-picker): randomly split a class into groups
- [Score Tracker](https://ironstamp.github.io/scoretracker): live projected scores with a countdown timer
- [Tarsia Puzzle Generator](https://ironstamp.github.io/tarsia-generator): printable triangle-matching puzzles
- [Domino Chain Puzzle Generator](https://ironstamp.github.io/domino-puzzle-generator): printable domino-chain matching cards

## Related

- **drive-backup** (private repo): scripts to back up a Google Drive to Office files, plus `classroom_archive.py` for archiving a course's attachments

---

*Made by Paul Carter, mathematics teacher, New Zealand.*
