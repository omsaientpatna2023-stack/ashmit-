<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>My Notes</title>

  <style>
    * {
      box-sizing: border-box;
    }

    body {
      margin: 0;
      font-family: Arial, sans-serif;
      background: #f2f4f7;
      color: #222;
    }

    header {
      background: #4f46e5;
      color: white;
      padding: 20px;
      text-align: center;
    }

    .container {
      max-width: 900px;
      margin: 25px auto;
      padding: 0 15px;
    }

    .search {
      width: 100%;
      padding: 14px;
      border: 1px solid #ddd;
      border-radius: 10px;
      font-size: 16px;
      margin-bottom: 15px;
    }

    .new-note {
      width: 100%;
      padding: 14px;
      background: white;
      border: 2px dashed #aaa;
      border-radius: 10px;
      cursor: pointer;
      font-size: 16px;
      margin-bottom: 20px;
    }

    .new-note:hover {
      background: #f8f8ff;
    }

    #notes {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
      gap: 15px;
    }

    .note {
      background: white;
      padding: 18px;
      border-radius: 12px;
      box-shadow: 0 3px 10px rgba(0,0,0,0.08);
    }

    .note h3 {
      margin-top: 0;
    }

    .note p {
      white-space: pre-wrap;
      color: #555;
    }

    .buttons {
      display: flex;
      gap: 8px;
      margin-top: 15px;
    }

    button {
      border: none;
      padding: 9px 13px;
      border-radius: 7px;
      cursor: pointer;
    }

    .edit {
      background: #e0e7ff;
    }

    .delete {
      background: #fee2e2;
    }

    .empty {
      text-align: center;
      color: #777;
      grid-column: 1 / -1;
    }
  </style>
</head>

<body>

  <header>
    <h1>📝 My Notes</h1>
    <p>Your notes are saved automatically</p>
  </header>

  <div class="container">

    <input
      id="search"
      class="search"
      type="text"
      placeholder="🔍 Search notes..."
    >

    <button class="new-note" onclick="addNote()">
      ➕ Create a new note
    </button>

    <div id="notes"></div>

  </div>

  <script>
    let notes = JSON.parse(localStorage.getItem("myNotes")) || [];

    function saveNotes() {
      localStorage.setItem("myNotes", JSON.stringify(notes));
    }

    function addNote() {
      const title = prompt("Enter note title:");

      if (title === null) return;

      const text = prompt("Write your note:");

      if (text === null) return;

      notes.push({
        id: Date.now(),
        title: title || "Untitled",
        text: text
      });

      saveNotes();
      displayNotes();
    }

    function editNote(id) {
      const note = notes.find(n => n.id === id);

      if (!note) return;

      const newTitle = prompt("Edit title:", note.title);

      if (newTitle === null) return;

      const newText = prompt("Edit note:", note.text);

      if (newText === null) return;

      note.title = newTitle || "Untitled";
      note.text = newText;

      saveNotes();
      displayNotes();
    }

    function deleteNote(id) {
      if (!confirm("Delete this note?")) return;

      notes = notes.filter(note => note.id !== id);

      saveNotes();
      displayNotes();
    }

    function displayNotes() {
      const notesContainer = document.getElementById("notes");
      const searchText = document
        .getElementById("search")
        .value
        .toLowerCase();

      const filteredNotes = notes.filter(note =>
        note.title.toLowerCase().includes(searchText) ||
        note.text.toLowerCase().includes(searchText)
      );

      notesContainer.innerHTML = "";

      if (filteredNotes.length === 0) {
        notesContainer.innerHTML =
          '<p class="empty">No notes found.</p>';
        return;
      }

      filteredNotes.forEach(note => {
        const noteElement = document.createElement("div");
        noteElement.className = "note";

        noteElement.innerHTML = `
          <h3>${escapeHTML(note.title)}</h3>
          <p>${escapeHTML(note.text)}</p>

          <div class="buttons">
            <button class="edit" onclick="editNote(${note.id})">
              ✏️ Edit
            </button>

            <button class="delete" onclick="deleteNote(${note.id})">
              🗑️ Delete
            </button>
          </div>
        `;

        notesContainer.appendChild(noteElement);
      });
    }

    function escapeHTML(text) {
      const div = document.createElement("div");
      div.textContent = text;
      return div.innerHTML;
    }

    document
      .getElementById("search")
      .addEventListener("input", displayNotes);

    displayNotes();
  </script>

</body>
</html>
