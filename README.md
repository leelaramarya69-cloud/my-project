<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>AI Agent</title>

<style>

body{
font-family:Arial;
background:#0f172a;
color:white;
margin:0;
}

header{
background:#1e293b;
padding:15px;
display:flex;
justify-content:space-between;
}

button{
background:#3b82f6;
border:none;
color:white;
padding:8px 12px;
cursor:pointer;
}

#chat{
height:70vh;
overflow:auto;
padding:15px;
}

.msg{
margin:10px 0;
padding:10px;
border-radius:8px;
max-width:70%;
}

.user{
background:#2563eb;
margin-left:auto;
}

.ai{
background:#334155;
}

#inputArea{
display:flex;
padding:10px;
background:#1e293b;
}

input{
flex:1;
padding:10px;
border:none;
outline:none;
}

#settings{
position:fixed;
top:0;
right:-350px;
width:350px;
height:100%;
background:#020617;
padding:20px;
transition:0.3s;
}

#settings.show{
right:0;
}

</style>
</head>

<body>

<header>
<h2>AI Agent</h2>
<button onclick="toggleSettings()">⚙ Settings</button>
</header>

<div id="chat"></div>

<div id="inputArea">
<input id="message" placeholder="Type message...">
<button onclick="send()">Send</button>
</div>

<div id="settings">

<h3>Settings</h3>

<label>OpenRouter API Key</label>
<input id="apikey">

<br><br>

<label>Model ID</label>
<input id="model" value="openai/gpt-4o-mini">

<br><br>

<button onclick="saveSettings()">Save</button>

</div>

<script>

let apiKey=localStorage.getItem("api")||""
let model=localStorage.getItem("model")||"openai/gpt-4o-mini"

document.getElementById("apikey").value=apiKey
document.getElementById("model").value=model

function toggleSettings(){
document.getElementById("settings").classList.toggle("show")
}

function saveSettings(){

apiKey=document.getElementById("apikey").value
model=document.getElementById("model").value

localStorage.setItem("api",apiKey)
localStorage.setItem("model",model)

alert("Saved")

}

function addMsg(text,type){

let div=document.createElement("div")
div.className="msg "+type
div.innerText=text

document.getElementById("chat").appendChild(div)

document.getElementById("chat").scrollTop=999999

}

async function send(){

let input=document.getElementById("message")
let text=input.value

if(!text)return

addMsg(text,"user")

input.value=""

addMsg("Typing...","ai")

let res=await fetch("https://openrouter.ai/api/v1/chat/completions",{

method:"POST",

headers:{
"Authorization":"Bearer "+apiKey,
"Content-Type":"application/json"
},

body:JSON.stringify({

model:model,

messages:[
{role:"user",content:text}
]

})

})

let data=await res.json()

document.querySelectorAll(".ai").forEach(e=>{
if(e.innerText=="Typing...")e.remove()
})

addMsg(data.choices[0].message.content,"ai")

}

</script>

</body>
</html>
