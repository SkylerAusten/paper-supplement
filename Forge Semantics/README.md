# Sending vs. Sharing Files
This model is intended to model the real-world process of sending & sharing files, specifically to illustrate the difference in sending a file on a computer versus sharing something like a Google Doc in the ability to see updates made to the shared/sent files (e.g. emailed files will not reflect changes, shared Google Docs will).
A generalized version of the model can be found in `file_sharing.frg`.  A version that will produce the trace shown in the paper can be found in `file_sharing_paper_trace.frg`.  Tests of the model are located in `file_sharing_tests.frg`.

## Model Sigs
#### `Person`
This is a sig representing email users.  Two specific named Persons are used, Bobbi and Joe.

#### `Item`
In this model, Items can be files and folders.  Folders can also contain a set of files.

#### `Location`
This is a sig representing locations where Items can be located (e.g. Bobbi's Google Drive or Joe's Computer).  The email server is a special location reserved for item copies created in the process of emailing a file or folder.

#### `Email` & `EmailContent`
Emails contain a single sender (from) and require one or more recipients (to) in order to be sent.  They must also have one content: a link (pointing to a Drive item), an attachment (of a computer item), or email text content.

#### `Inbox`
Each user in the system has one inbox, which contains sent, drafted, and received emails, as well as threads created by replying to emails.

## Transitions

### `doNothing`
No action is taken. Used to represent system stasis or final states. No properties or relations are modified.

### `createFile`
Creates a new file in a location owned by the actor. The file is unshared, has no `same_content` links, and is attributed to the actor as both owner and creator.

### `createFolder`
Creates a new, initially empty folder in a location owned by the actor. The folder is unshared and attributed to the actor.

### `moveItem`
Moves a file into a folder, assuming both are owned by the actor and in the same location. Updates the folder’s contents and inherits the folder’s `shared_with` set for the moved item.

### `createEmail`
Creates a new email draft in the actor’s inbox. The draft starts with no recipients or content.

### `setRecipients`
Sets the `to` field for an email draft. Server cannot be a recipient. The email must still be in the actor’s drafts.

### `removeRecipients`
Clears all recipients from an email draft that currently has recipients.

### `attachFile`
Attaches a file from the actor’s computer to an email draft. A new copy is created on the email server and linked via `same_content`. The original file is not modified.

### `attachFolder`
Attaches a folder (and its contents) from the actor’s computer to an email draft. Clones of the folder and its files are created on the email server, maintaining `same_content` links to the originals.

### `addText`
Adds plain text to an email draft that currently has no content.

### `addLink`
Inserts a hyperlink to a Drive item (owned by or shared with the actor) into an email draft. The link points directly to the item and enables shared access on send.

### `removeEmailContent`
Removes the content from an email draft and deletes any associated email content object (text, attachment, or link).

### `sendEmail`
Sends a drafted email. Moves the email to the sender’s `sent` and recipients’ `received` folders. If the email contains a link, the linked item(s) are shared with recipients, and `shared_with_me` sets are updated accordingly.

### `sendReply`
Sends a reply to a previously received email. The reply is threaded to the original and delivered to the original sender. Sharing semantics apply if the content includes a link.

### `editFile`
Simulates editing a file by breaking its `same_content` links, reflecting that it has diverged from related copies.

### `downloadFileAttachment`
Downloads a file that was attached to an email (in `sent`, `received`, or `drafts`). A new local file is created on the actor's computer, linked via `same_content` to the original attachment, and attributed to the original creator.

### `downloadDriveFile`
Downloads a file from the actor’s Drive or one shared with them. Creates a new file on their computer, linked to the source via `same_content`, and owned by the actor.

### `uploadFileToDrive`
Uploads a file from the actor’s computer to their Drive. A new Drive-based file is created, linked to the original via `same_content`, and owned by the actor.

### `duplicateFile`
Creates a duplicate of an existing file (owned by or shared with the actor) in the same location. The duplicate is attributed to the actor, and connected to the original via `same_content`.

## Visualizing States & Traces

### Sterling (`theme.json`)
When running any traces, Sterling is the default visualizer. Use `theme.json` to hide some of the less helpful relations & atoms.

### Cope and Drag
The images presented in the paper use Cope and Drag (CnD). It can be accessed via the Layout tab in Sterling.  The CnD specs used can be found in the `CnD Specs` folder.