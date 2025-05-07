DOCTYPE html>
<html lang="id">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Forum Pemeliharaan</title>
  <style>
    body {
      font-family: sans-serif;
      margin: 0;
      padding: 20px;
      background-color: #f9fafb;
    }
    .container {
      max-width: 600px;
      margin: auto;
    }
    .card {
      background: white;
      padding: 16px;
      margin-bottom: 16px;
      border-radius: 12px;
      box-shadow: 0 2px 4px rgba(0,0,0,0.05);
    }
    .title {
      font-size: 1.5em;
      margin-bottom: 0.5em;
    }
    .button {
      background-color: #4f46e5;
      color: white;
      border: none;
      padding: 10px 16px;
      border-radius: 8px;
      cursor: pointer;
    }
    .button:hover {
      background-color: #4338ca;
    }
    .input, textarea {
      width: 100%;
      padding: 8px;
      margin-top: 8px;
      margin-bottom: 16px;
      border: 1px solid #d1d5db;
      border-radius: 8px;
    }
    .reply {
      background-color: #f3f4f6;
      padding: 8px;
      margin: 4px 0;
      border-radius: 6px;
    }
    .meta {
      font-size: 0.85em;
      color: #6b7280;
      margin-bottom: 8px;
    }
  </style>
</head>
<body>
  <div class="container">
    <h1 class="title">Forum Pemeliharaan</h1>

    <input id="search" class="input" placeholder="Cari topik..." oninput="renderTopics()" />

    <div class="card">
      <h2>Buat Topik Baru</h2>
      <input id="author" class="input" placeholder="Nama Anda" />
      <input id="title" class="input" placeholder="Judul topik" />
      <textarea id="content" class="input" placeholder="Isi topik"></textarea>
      <input id="files" class="input" type="file" multiple />
      <button class="button" onclick="postTopic()">Posting</button>
    </div>

    <div id="topics"></div>
  </div>

  <script>
    const topics = [];

    function formatDate(date) {
      return new Date(date).toLocaleString('id-ID');
    }

    function renderTopics() {
      const container = document.getElementById('topics');
      const query = document.getElementById('search').value.toLowerCase();
      container.innerHTML = '';

      const filtered = topics.filter(topic =>
        topic.title.toLowerCase().includes(query)
      );

      if (filtered.length === 0) {
        container.innerHTML = '<p>Tidak ada topik yang ditemukan.</p>';
        return;
      }

      filtered.forEach((topic, index) => {
        const card = document.createElement('div');
        card.className = 'card';

        const filesHtml = topic.files.length
          ? `<div class="meta"><strong>Lampiran:</strong> ${topic.files.join(', ')}</div>`
          : '';

        card.innerHTML = `
          <h3>${topic.title}</h3>
          <div class="meta">Ditulis oleh <strong>${topic.author}</strong> pada ${formatDate(topic.createdAt)}</div>
          <p>${topic.content}</p>
          ${filesHtml}
          <div><strong>Balasan:</strong></div>
          ${topic.replies.map(r => `<div class="reply"><strong>${r.author}</strong> (${formatDate(r.time)}): ${r.text}</div>`).join('')}
          <input id="replier-${index}" class="input" placeholder="Nama Anda" />
          <textarea id="reply-${index}" class="input" placeholder="Tulis balasan..."></textarea>
          <button class="button" onclick="addReply(${index})">Balas</button>
        `;
        container.appendChild(card);
      });
    }

    function postTopic() {
      const author = document.getElementById('author').value.trim();
      const title = document.getElementById('title').value.trim();
      const content = document.getElementById('content').value.trim();
      const fileInput = document.getElementById('files');
      const fileNames = Array.from(fileInput.files).map(f => f.name);

      if (author && title && content) {
        topics.unshift({
          author,
          title,
          content,
          createdAt: new Date(),
          files: fileNames,
          replies: []
        });
        document.getElementById('author').value = '';
        document.getElementById('title').value = '';
        document.getElementById('content').value = '';
        fileInput.value = '';
        renderTopics();
      }
    }

    function addReply(index) {
      const textarea = document.getElementById(`reply-${index}`);
      const name = document.getElementById(`replier-${index}`);
      const replyText = textarea.value.trim();
      const author = name.value.trim();

      if (author && replyText) {
        topics[index].replies.push({
          author,
          text: replyText,
          time: new Date()
        });
        textarea.value = '';
        name.value = '';
        renderTopics();
      }
    }

    renderTopics();
  </script>
</body>
</html>