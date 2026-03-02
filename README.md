<!DOCTYPE html>
<html lang="th">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>MSU MakeFriend</title>
<link href="https://fonts.googleapis.com/css2?family=Prompt:wght@300400;600&display=swap" rel="stylesheet">

<style>
*{box-sizing:border-box;margin:0;padding:0;font-family:'Prompt',sans-serif;}

body{
  background:linear-gradient(135deg,#667eea,#764ba2);
  min-height:100vh;
  color:white;
}

header{text-align:center;padding:40px 20px 20px;}

.container{width:90%;max-width:1100px;margin:auto;margin-bottom:60px;}

.hidden{display:none;}

button{
  padding:12px 20px;
  border:none;
  border-radius:12px;
  cursor:pointer;
  margin-top:15px;
  background:white;
  color:#333;
  font-weight:600;
  transition:.3s;
}
button:hover{transform:scale(1.05);}

.back-btn{background:rgba(0,0,0,.4);color:white;}

.grid{
  display:grid;
  grid-template-columns:repeat(auto-fit,minmax(250px,1fr));
  gap:20px;
  margin-top:30px;
}

.card{
  backdrop-filter:blur(15px);
  background:rgba(255,255,255,.15);
  border-radius:20px;
  padding:25px;
  text-align:center;
  cursor:pointer;
  transition:.3s;
}
.card:hover{
  transform:translateY(-8px);
  background:rgba(255,255,255,.25);
}

/* Quiz */
.question{
  margin:20px 0;
  padding:20px;
  background:rgba(255,255,255,.15);
  border-radius:20px;
}
label{display:block;margin:6px 0;cursor:pointer;}

/* Post */
textarea{
  width:100%;
  max-width:600px;
  height:100px;
  border-radius:15px;
  padding:15px;
  border:none;
  margin-top:20px;
}

.file-input{
  margin-top:10px;
  color:white;
}

.post{
  backdrop-filter:blur(15px);
  background:rgba(255,255,255,.15);
  padding:20px;
  border-radius:20px;
  margin:15px auto;
  max-width:600px;
  display:flex;
  gap:15px;
  align-items:center;
}

.post img{
  width:70px;
  height:70px;
  border-radius:50%;
  object-fit:cover;
}

.post-text{
  flex:1;
}

.thank-you{
  margin-top:15px;
  background:rgba(0,0,0,.4);
  padding:10px 20px;
  border-radius:15px;
  display:inline-block;
}
</style>
</head>

<body>

<header>
<h1>🌟 MSU MakeFriend 🌟</h1>
<p>หาเพื่อนใหม่ตามคณะและสาขา</p>
</header>

<div class="container">

<div id="quizSection">
<h2 style="text-align:center;">🎯 แบบสอบถามสไตล์เพื่อนของคุณ</h2>
<form id="quizForm"></form>
<div style="text-align:center;">
<button onclick="submitQuiz()">เริ่มหาเพื่อน 🚀</button>
</div>
</div>

<div id="facultySection" class="grid hidden"></div>
<div id="majorSection" class="grid hidden"></div>
<div id="makeFriendSection" class="hidden" style="text-align:center;"></div>

</div>

<script>

const quizData=[
{q:"1) เวลาเจอคนใหม่คุณเป็นแบบไหน?",options:["เข้าหาก่อน","รอให้ทัก","คุยนิดหน่อย","เขินแต่จริงใจ"]},
{q:"2) คุณอยากได้เพื่อนแบบไหน?",options:["สายฮา","สายเรียน","สายกิจกรรม","เข้าใจเรา"]},
{q:"3) เวลาว่างคุณชอบทำอะไร?",options:["ดูหนัง","เล่นเกม","ออกกำลังกาย","คาเฟ่"]},
{q:"4) ถ้าเพื่อนชวนเที่ยวกระทันหัน?",options:["ไปเลย","คิดก่อน","ดูเงินก่อน","ปฏิเสธ"]},
{q:"5) สิ่งสำคัญของคำว่าเพื่อนคือ?",options:["จริงใจ","สนุก","ช่วยเหลือ","เข้าใจ"]},
{q:"6) ถ้าทะเลาะกับเพื่อนจะ?",options:["ง้อก่อน","รอคุย","คุยตรงๆ","ปล่อยเวลา"]},
{q:"7) คุณเลือกแบบไหน?",options:["เพื่อนเยอะ","เพื่อนน้อยแต่สนิท","กลุ่มใหญ่","มีคนเดียวก็พอ"]}
];

const quizForm=document.getElementById("quizForm");

quizData.forEach((item,i)=>{
let div=document.createElement("div");
div.className="question";
let html=`<h3>${item.q}</h3>`;
item.options.forEach(opt=>{
html+=`<label><input type="radio" name="q${i}" required> ${opt}</label>`;
});
div.innerHTML=html;
quizForm.appendChild(div);
});

function submitQuiz(){
if(!quizForm.checkValidity()){
alert("กรุณาตอบให้ครบก่อน 😊");
return;
}
document.getElementById("quizSection").classList.add("hidden");
facultySection.classList.remove("hidden");
showFaculties();
}

const data={ "คณะมนุษยศาสตร์และสังคมศาสตร์📚":[ "ประวัติศาสตร์","จีน","ไทย","ยุโรป","อังกฤษ", "อาเซียน","เอเชีย","ภูมิศาสตร์", "สื่อสารมวลชนและเอกสาร","การพัฒนาชุมชน"], "คณะวิทยาศาสตร์🔬": [ "คณิตศาสตร์","เคมี","จุลชีววิทยา","ชีวเคมี", "ชีววิทยา","เทคโนโลยีพลังงาน","ฟิสิกส์","สถิติ"], "คณะวิศวกรรมศาสตร์⚙️": [ "วิศวกรรมทั่วไป","วิศวกรรมเครื่องกล","วิศวกรรมไฟฟ้า", "วิศวกรรมเมคคาทรอนิกส์","วิศวกรรมยานยนต์", "วิศวกรรมโยธา","วิศวกรรมระบบราง", "วิศวกรรมสิ่งแวดล้อม","วิศวกรรมอาหาร"], "คณะพยาบาลศาสตร์🩹": ["พยาบาลศาสตร์"], "คณะศึกษาศาสตร์🖌️": [ "วิทยาศาสตร์การกีฬา","จิตวิทยา","การศึกษาปฐมวัย", "คณิตศาสตร์","เทคโนโลยีและสื่อสารการศึกษา", "ภาษาไทยและภาษาต่างประเทศ","สังคมศึกษา", "สุขศึกษา พลศึกษา และนันทนาการ","วิทยาศาสตร์ทั่วไป"], "คณะเภสัชศาสตร์💊": ["การบริบาลทางเภสัชกรรม"], "คณะเทคโนโลยี💻": [ "เทคโนโลยีการอาหาร","เทคโนโลยีชีวภาพ", "ประมง","พืชไร่นา","พืชสวน","สัตวศาสตร์"], "คณะการบัญชีและการจัดการ💰": [ "การเงิน","การจัดการ","การบัญชี","การตลาด", "คอมพิวเตอร์ธุรกิจ","บริหารธุรกิจ", "ระบบสารสนเทศ","เศรษฐศาสตร์"], "คณะการท่องเที่ยวและการโรงแรม🌍": [ "การท่องเที่ยวและการโรงแรม"], "คณะสถาปัตยกรรมศาสตร์ ผังเมืองและนฤมิตศิลป์🗿": [ "การออกแบบนิเทศศิลป์","ภูมิสถาปัตยกรรม", "วางแผนภาคและเมือง","สถาปัตยกรรม", "สถาปัตยกรรมภายใน"], "คณะวิทยาการสารสนเทศ🖥️": [ "เทคโนโลยีสารสนเทศ","วิทยาการคอมพิวเตอร์", "วิทยาการ วิทยาศาสตร์ข้อมูล","นิเทศศาสตร์"], "วิทยาลัยการเมืองการปกครอง🕯️": [ "การปกครอง","รัฐประศาสนศาสตร์","รัฐศาสตร์"], "คณะสาธารณสุขศาสตร์🦠": [ "โภชนาการ","สาธารณสุข"], "คณะแพทยศาสตร์🩺": [ "แพทยศาสตร์","การแพทย์แผนไทยประยุกต์", "ปฏิบัติการฉุกเฉินการแพทย์"], "คณะสิ่งแวดล้อมและทรัพยากรศาสตร์🌳": [ "การจัดการทรัพยากรธรรมชาติและสิ่งแวดล้อม", "เทคโนโลยีสิ่งแวดล้อม","ครุศาสตร์"], "วิทยาลัยดุริยางคศิลป์🎼": ["ดุริยางคศิลป์"], "คณะสัตวแพทยศาสตร์🐶": ["สัตวแพทยศาสตร์"], "คณะนิติศาสตร์⚖️": ["นิติศาสตร์"], "คณะศิลปกรรมศาสตร์และวัฒนธรรมศาสตร์👨🏻‍🎨": [ "การออกแบบ","ทัศนศิลป์", "ศิลปะการแสดง","ศิลปวัฒนธรรม"] };
const facultySection=document.getElementById("facultySection");
const majorSection=document.getElementById("majorSection");
const makeFriendSection=document.getElementById("makeFriendSection");

let currentMajor="";

function showFaculties(){
facultySection.innerHTML="";
majorSection.classList.add("hidden");
makeFriendSection.classList.add("hidden");

for(let faculty in data){
let div=document.createElement("div");
div.className="card";
div.innerText=faculty;
div.onclick=()=>showMajors(faculty);
facultySection.appendChild(div);
}
}

function showMajors(faculty){
facultySection.classList.add("hidden");
majorSection.classList.remove("hidden");
majorSection.innerHTML="";

data[faculty].forEach(major=>{
let div=document.createElement("div");
div.className="card";
div.innerText=major;
div.onclick=()=>showMakeFriend(major);
majorSection.appendChild(div);
});

let back=document.createElement("button");
back.innerText="ย้อนกลับ";
back.className="back-btn";
back.onclick=showFaculties;
majorSection.appendChild(back);
}

function showMakeFriend(major){
currentMajor=major;
majorSection.classList.add("hidden");
makeFriendSection.classList.remove("hidden");

makeFriendSection.innerHTML=`
<h2>${major}</h2>
<textarea id="userText" placeholder="แนะนำตัวได้เลย 🎉"></textarea><br>
<input type="file" id="userImage" accept="image/*" class="file-input"><br>
<button onclick="postMessage()">โพสต์</button>
<div id="posts"></div>
<br>
<button class="back-btn" onclick="showFaculties()">กลับหน้าหลัก</button>
`;
loadPosts();
}

function postMessage(){
let text=document.getElementById("userText").value;
let file=document.getElementById("userImage").files[0];

if(text.trim()==="" && !file) return;

if(file){
let reader=new FileReader();
reader.onload=function(e){
savePost(text,e.target.result);
};
reader.readAsDataURL(file);
}else{
savePost(text,null);
}
}

function savePost(text,image){
let posts=JSON.parse(localStorage.getItem(currentMajor))||[];
posts.push({text:text,image:image});
localStorage.setItem(currentMajor,JSON.stringify(posts));

document.getElementById("userText").value="";
document.getElementById("userImage").value="";
loadPosts();
showThankYou();
}

function loadPosts(){
let postsDiv=document.getElementById("posts");
postsDiv.innerHTML="";
let posts=JSON.parse(localStorage.getItem(currentMajor))||[];

posts.forEach(post=>{
let div=document.createElement("div");
div.className="post";

if(post.image){
let img=document.createElement("img");
img.src=post.image;
div.appendChild(img);
}

let textDiv=document.createElement("div");
textDiv.className="post-text";
textDiv.innerText=post.text;

div.appendChild(textDiv);
postsDiv.prepend(div);
});
}

function showThankYou(){
let message=document.createElement("div");
message.className="thank-you";
message.innerHTML="✅ ขอบคุณที่เข้ามาร่วมเล่นกับเรา ❤";
makeFriendSection.insertBefore(message, makeFriendSection.children[3]);
setTimeout(()=>{message.remove();},3000);
}

</script>

</body>
</html>
