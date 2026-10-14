# one — learn together, beside the code

**one** is a VS Code extension for hands-on programming lessons: a teacher and student can connect through a private browser invitation, edit a shared project, request C++ compilation and execution, and draw on a whiteboard beside the code.

## Why I built it

While developing [Ember Valley](https://github.com/KshetriDeepak/ember-valley), I encountered more ideas and programming concepts than I could comfortably understand on my own. I began taking one-to-one Zoom lessons with teachers from different parts of the world.

Those lessons helped, but I found it difficult to learn programming through video and verbal explanations alone. I wanted my teacher and me to work with the same code, see compiler output, and sketch an idea beside the program while discussing it.

That need led me to build **one**: a shared learning space that connects the code, the compiler and the whiteboard. It grew out of a real difficulty in my own learning process while building a game.

## Install the development preview

Download **one-0.7.2.vsix** from [Releases](https://github.com/KshetriDeepak/one/releases). In VS Code, open Extensions, select the menu, choose **Install from VSIX…**, and select the downloaded file. The bundled host helpers target macOS; the other participant joins through a browser. C++ runs require clang++ on the host.

1. Open the project folder you want to share in VS Code.
2. Run **one: Share Project** and send the complete invitation to your teacher.
3. The teacher opens it in a browser and enters their name.
4. Edit together, open the whiteboard, and request **Run C++** when ready. The host approves each run.
5. Stop sharing when the lesson ends to revoke the invitation.

The **one-source-0.7.2.zip** release attachment contains the source and bundled dependencies/helpers. This is a development preview. AI explanations are disabled; no participant receives access to the host’s Codex or other AI account. Participants may use their own AI tools separately.

The complete source is the **one-source-0.7.2.zip release attachment**. GitHub’s automatically generated source archives contain only this showcase repository.

## Development

Node.js 18 or newer is required for the local checks. The included dependencies can be restored with `npm ci`. Run `npm test` for the AI-disconnection and local sharing regression checks. Run `python3 package-extension.py` to rebuild the VSIX from the source package.

## Project sharing and execution

Version 0.4 removes screen capture, desktop input and remote shell access from the extension. Restart VS Code after installing, then click **one · Share project**. Copy the complete teacher link from the sharing panel. Stopping sharing revokes the link.

The teacher can list, read and edit existing C++ source/header, Markdown and text files in the single shared project. Hidden paths, symbolic links outside the project, and files over 750 KB are rejected. Edits check document revisions to avoid silently overwriting the student's changes.

**Run C++** requests approval in VS Code. Review the selected file and choose **Run once**. The approved source snapshot is compiled with clang++ (C++17) and run with a 30-second limit. Output appears in the browser and the **Code Lesson · Run output** channel. Interactive stdin and multi-file build systems are not supported in this version.

Code runs with the student's Mac permissions. This is not a sandbox: approved code can access files or the network. No automatic run, arbitrary shell command, screen capture, or mouse/keyboard endpoint is exposed. No macOS Screen Recording or Accessibility permission is needed by this version; previous grants can be removed in System Settings.

Keep Zoom for calls. Use **Open teacher view** to preview the browser editor, not Live Share or vscode.dev. The browser hides the private key after connecting, so use **Copy teacher link** in VS Code when inviting someone else.

## Encryption in transit (0.4.1)

Public teacher links require HTTPS. The app rejects public requests unless Cloudflare reports HTTPS, sends HTTP Strict Transport Security on HTTPS responses, and refuses API redirects. Teacher links and public availability checks accept HTTPS only.

The browser-to-Cloudflare leg uses TLS; Cloudflare-to-cloudflared uses an encrypted tunnel. Only the connection from cloudflared to the extension on 127.0.0.1 uses HTTP, entirely inside this Mac. This is transport encryption, not end-to-end encryption: Cloudflare terminates TLS and can process plaintext traffic. The private invitation key is authentication, not an encryption key. No new certificate installation is needed.

## Live editing (0.4.5)
Browser edits are sent in 120 ms batches even during continuous typing; network and saving time add latency. Authenticated change notifications return unsaved VS Code edits without repeatedly fetching unchanged files. Different, non-overlapping edit regions merge automatically. Overlapping edits pause synchronization and preserve the browser draft for manual resolution; copy it before reloading. This is revision-checked synchronization, not a CRDT. Run C++ stays disabled while browser changes are unsynced. Restart VS Code after installing this version, start sharing, and use the newly generated link.

## PDF viewing (0.4.3)
PDFs in the shared project appear in the file list and open in a read-only browser preview (up to 25 MB). Open PDF/download is available if the browser cannot embed PDFs. Files require the same HTTPS and invitation authentication as code, and cannot be edited or run. Downloaded or already-loaded files cannot be recalled after sharing stops. This viewer is part of Code Lesson, not Live Share.

## Live cursors (0.4.8)
Teacher display names follow colored carets and selection highlights in VS Code. The browser shows the host’s active selection and other browser participants in the same file. Cursor-only changes are exchanged about every 100 ms plus network delay; typing sends selection positions with each edit. Positions are tied to a document version to avoid highlighting the wrong text during conflicts. Departed browser participants expire after eight seconds. This applies to the code editor, not PDF objects or desktop controls.

## Shared whiteboard (0.5.0)
The browser has a whiteboard beside the code. On the Mac, choose **one: Open Whiteboard Beside Code**, or use the button in the sharing panel. Both views share pen/highlighter strokes, lines, arrows, rectangles and ellipses. Choose color and thickness; Undo, Redo, Eraser and Clear mine affect your marks. Save PNG exports a snapshot. Drawings are kept only for the current sharing session and are removed when sharing ends. No draggable explanation window is included.

Whiteboard code copies: choose **Copy code onto board** to copy selected text (or the whole open file) into a shared, static card. Both editors can draw over it. For longer files, select the section you want to discuss. Source code is unchanged.

## AI availability (0.7.2)

The Explain feature is disconnected. one does not launch Codex, read Codex login credentials, call an AI provider, or send code, PDFs or whiteboard content to an AI account. Local and browser explanation requests are rejected before context collection. The Explain controls show that AI access is disconnected.

Project sharing, editing, whiteboard drawing/export and voice calls remain available. Each participant can use their own separately signed-in AI application outside one. No shared host AI account or automatic AI setup is included. The extension does not manage personal AI sign-ins.

## Voice calls

Click **Join voice** in the shared browser page. On the host, use **Join voice** in the sharing or whiteboard panel (or **one: Join Voice Call**) to open a dedicated browser call page. Click Join voice there and allow the microphone. Keep that page open while using VS Code. Mute disables the outgoing audio track; Leave call closes connections and releases the microphone. Up to four participants can join. No audio is recorded by one.

Voice uses WebRTC encrypted audio connections with authenticated HTTPS signaling. Google STUN is used for connection discovery; no TURN relay is configured in this release, so some restrictive networks or mobile networks may not connect. The server's createVoice options accept a standard iceServers list for a future managed relay. The app reports connection failure instead of claiming the call is connected. Stop sharing causes clients to leave on the next failed session check (within about 10 seconds); closing the browser releases the microphone immediately, and stale members expire after 15 seconds.
