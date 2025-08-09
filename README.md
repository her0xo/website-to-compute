 <!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>موقع h.er0x - الأخبار والعروض</title>
<style>
  * { box-sizing: border-box; }
  body {
    font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
    margin: 0; padding: 0 10px;
    background-color: #f9f9f9;
    color: #222;
  }
  header {
    background-color: #222;
    color: #fff;
    text-align: center;
    padding: 15px 0;
    font-size: 24px;
    font-weight: bold;
    user-select: none;
  }
  .screen {
    max-width: 600px;
    margin: 20px auto;
  }
  .hidden { display: none; }
  input[type="text"],
  input[type="password"],
  input[type="file"] {
    width: 100%;
    padding: 10px;
    margin: 8px 0 15px 0;
    border: 1px solid #ccc;
    border-radius: 5px;
    font-size: 16px;
  }
  button {
    background-color: #222;
    color: #fff;
    border: none;
    padding: 12px 20px;
    border-radius: 5px;
    cursor: pointer;
    font-size: 16px;
    user-select: none;
    transition: background-color 0.3s ease;
  }
  button:hover { background-color: #555; }
  .error-msg { color: red; font-size: 14px; }
  #posts-container { margin-top: 10px; }
  .post {
    background-color: #fff;
    border: 1px solid #ddd;
    margin-bottom: 12px;
    padding: 10px;
    border-radius: 6px;
    position: relative;
    word-break: break-word;
  }
  .post-desc { margin-bottom: 4px; font-weight: bold; }
  .post-date { font-size: 12px; color: #666; margin-top: 2px; margin-bottom: 6px; }
  .post-link {
    color: #0077cc;
    text-decoration: none;
    word-break: break-word;
  }
  .post-img {
    max-width: 100%;
    max-height: 150px;
    margin: 10px 0;
    border-radius: 4px;
  }
  .emoji-reactions { margin-top: 10px; user-select: none; }
  .emoji-btn {
    font-size: 24px;
    margin: 0 5px;
    cursor: pointer;
    user-select: none;
    transition: transform 0.15s ease;
  }
  .emoji-btn:active { transform: scale(1.3); }
  .delete-post-btn {
    position: absolute;
    top: 10px;
    left: 10px;
    background-color: #c00;
    font-size: 14px;
    padding: 4px 8px;
    border-radius: 4px;
  }
  #link-share {
    margin: 25px auto 40px auto;
    max-width: 600px;
    background: #eee;
    padding: 10px;
    border-radius: 8px;
    user-select: all;
    text-align: center;
    font-size: 14px;
    color: #555;
    word-break: break-all;
  }
  #code-login-btn, #refresh-posts-btn {
    position: fixed;
    bottom: 20px;
    background-color: #222;
    color: #fff;
    border-radius: 8px;
    padding: 10px 15px;
    border: none;
    cursor: pointer;
    z-index: 9999;
    user-select: none;
    transition: background-color 0.3s ease;
  }
  #code-login-btn:hover, #refresh-posts-btn:hover { background-color: #555; }
  #code-login-btn { left: 20px; }
  #refresh-posts-btn { right: 20px; }
  @media screen and (max-width: 480px) {
    body { padding: 10px 5px; }
    header { font-size: 20px; }
    button { font-size: 14px; }
  }
</style>

<!-- Firebase -->
<script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-storage.js"></script>
<script>
  // ضع بيانات مشروعك هنا
  const firebaseConfig = {
    apiKey: "ضع-هنا",
    authDomain: "ضع-هنا.firebaseapp.com",
    databaseURL: "https://ضع-هنا.firebaseio.com",
    projectId: "ضع-هنا",
    storageBucket: "ضع-هنا.appspot.com",
    messagingSenderId: "ضع-هنا",
    appId: "ضع-هنا"
  };
  firebase.initializeApp(firebaseConfig);
  const db = firebase.database();
  const storage = firebase.storage();
</script>
</head>
<body>
<header>h.er0x</header>

<!-- تسجيل دخول -->
<div id="login-screen" class="screen">
  <h2>تسجيل دخول المشرف</h2>
  <input type="password" id="password-input" placeholder="أدخل كلمة السر" />
  <button id="login-btn">دخول</button>
  <p id="login-msg" class="error-msg"></p>
</div>

<!-- الصفحة الرئيسية -->
<div id="main-screen" class="screen hidden">
  <section id="post-management" class="hidden">
    <h2>إدارة المنشورات</h2>
    <input type="text" id="post-link" placeholder="رابط الفيديو أو الموقع (اختياري)" />
    <input type="text" id="post-desc" placeholder="وصف المنشور (اختياري)" />
    <input type="file" id="post-image" accept="image/*" />
    <button id="add-post-btn">إضافة منشور</button>
  </section>
  <section id="posts-section">
    <h2>المنشورات</h2>
    <div id="posts-container"></div>
  </section>
</div>

<button id="code-login-btn" title="تسجيل كود المشرف">تسجيل الكود</button>
<button id="refresh-posts-btn" title="تحديث المنشورات">تحديث</button>
<div id="link-share">رابط الموقع: <span id="site-url"></span></div>

<script>
  const PASSWORD = "CD2007M";
  const loginScreen = document.getElementById("login-screen");
  const mainScreen = document.getElementById("main-screen");
  const loginBtn = document.getElementById("login-btn");
  const passwordInput = document.getElementById("password-input");
  const loginMsg = document.getElementById("login-msg");
  const postManagement = document.getElementById("post-management");
  const postLinkInput = document.getElementById("post-link");
  const postDescInput = document.getElementById("post-desc");
  const postImageInput = document.getElementById("post-image");
  const addPostBtn = document.getElementById("add-post-btn");
  const postsContainer = document.getElementById("posts-container");
  const codeLoginBtn = document.getElementById("code-login-btn");
  const refreshPostsBtn = document.getElementById("refresh-posts-btn");
  const emojis = ["💔","🔥","👍","👎","👁️","☝️","🤦"];
  let posts = [];
  let reactions = {};
  let isAdmin = false;

  document.getElementById("site-url").textContent = window.location.href;

  loginBtn.addEventListener("click", () => {
    if(passwordInput.value.trim() === PASSWORD) {
      isAdmin = true;
      loginScreen.classList.add("hidden");
      mainScreen.classList.remove("hidden");
      postManagement.classList.remove("hidden");
      loadPosts();
    } else { loginMsg.textContent = "كلمة السر غير صحيحة"; }
  });

  window.addEventListener("load", () => {
    if(!isAdmin){
      loginScreen.classList.add("hidden");
      mainScreen.classList.remove("hidden");
      postManagement.classList.add("hidden");
      loadPosts();
    }
  });

  codeLoginBtn.addEventListener("click", () => {
    const inputCode = prompt("أدخل كود المشرف:");
    if(inputCode === PASSWORD){
      isAdmin = true;
      loginScreen.classList.add("hidden");
      mainScreen.classList.remove("hidden");
      postManagement.classList.remove("hidden");
      loadPosts();
      alert("تم تسجيل الدخول كمشرف!");
    } else alert("الكود غير صحيح");
  });

  refreshPostsBtn.addEventListener("click", () => {
    loadPosts();
    alert("تم تحديث المنشورات");
  });

  function saveData() {
    db.ref("posts").set(posts);
    db.ref("reactions").set(reactions);
  }

  function loadPosts() {
    db.ref("posts").once("value").then(snap => {
      posts = snap.val() || [];
      db.ref("reactions").once("value").then(snap2 => {
        reactions = snap2.val() || {};
        renderPosts();
      });
    });
  }

  function formatDateTime(dateStr) {
    const d = new Date(dateStr);
    if(isNaN(d)) return "تاريخ غير معروف";
    return `تم الإرسال الساعة ${String(d.getHours()).padStart(2,"0")}:${String(d.getMinutes()).padStart(2,"0")} يوم ${String(d.getDate()).padStart(2,"0")}/${String(d.getMonth()+1).padStart(2,"0")}/${d.getFullYear()}`;
  }

  function createPostElement(post, index) {
    const postDiv = document.createElement("div");
    postDiv.classList.add("post");
    const descP = document.createElement("p");
    descP.classList.add("post-desc");
    descP.textContent = post.desc || "بدون وصف";
    const dateP = document.createElement("p");
    dateP.classList.add("post-date");
    dateP.textContent = formatDateTime(post.timestamp);
    postDiv.appendChild(descP);
    postDiv.appendChild(dateP);
    if(post.link){
      const linkA = document.createElement("a");
      linkA.href = post.link; linkA.target = "_blank";
      linkA.classList.add("post-link");
      linkA.textContent = post.link;
      postDiv.appendChild(linkA);
    }
    if(post.image){
      const img = document.createElement("img");
      img.src = post.image; img.classList.add("post-img");
      postDiv.appendChild(img);
    }
    const emojiDiv = document.createElement("div");
    emojiDiv.classList.add("emoji-reactions");
    emojis.forEach(e => {
      const btn = document.createElement("span");
      btn.textContent = e;
      btn.classList.add("emoji-btn");
      btn.addEventListener("click", () => { addReaction(index, e); });
      emojiDiv.appendChild(btn);
      const countSpan = document.createElement("span");
      countSpan.id = `reaction-count-${index}-${e}`;
      countSpan.textContent = reactions[index]?.[e] || 0;
      countSpan.style.fontSize = "14px";
      countSpan.style.margin = "0 5px";
      emojiDiv.appendChild(countSpan);
    });
    postDiv.appendChild(emojiDiv);
    if(isAdmin){
      const delBtn = document.createElement("button");
      delBtn.textContent = "حذف";
      delBtn.classList.add("delete-post-btn");
      delBtn.addEventListener("click", () => {
        if(confirm("هل تريد حذف هذا المنشور؟")) {
          posts.splice(index,1);
          delete reactions[index];
          saveData();
          renderPosts();
        }
      });
      postDiv.appendChild(delBtn);
    }
    return postDiv;
  }

  function renderPosts() {
    postsContainer.innerHTML = "";
    posts.forEach((p, idx) => postsContainer.appendChild(createPostElement(p, idx)));
  }

  function addReaction(postIndex, emoji) {
    if(!reactions[postIndex]) reactions[postIndex] = {};
    if(!reactions[postIndex][emoji]) reactions[postIndex][emoji] = 0;
    reactions[postIndex][emoji]++;
    document.getElementById(`reaction-count-${postIndex}-${emoji}`).textContent = reactions[postIndex][emoji];
    saveData();
  }

  addPostBtn.addEventListener("click", () => {
    const link = postLinkInput.value.trim();
    const desc = postDescInput.value.trim();
    const file = postImageInput.files[0];
    if(!link && !desc && !file) return alert("يجب إدخال رابط أو وصف أو صورة");
    if(file){
      const imgRef = storage.ref("images/" + Date.now() + "-" + file.name);
      imgRef.put(file).then(snapshot => {
        snapshot.ref.getDownloadURL().then(url => {
          posts.unshift({link, desc, image: url, timestamp: new Date().toISOString()});
          saveData();
          renderPosts();
          postLinkInput.value = ""; postDescInput.value = ""; postImageInput.value = "";
        });
      });
    } else {
      posts.unshift({link, desc, image: null, timestamp: new Date().toISOString()});
      saveData();
      renderPosts();
      postLinkInput.value = ""; postDescInput.value = "";
    }
  });
</script>
</body>
</html>
