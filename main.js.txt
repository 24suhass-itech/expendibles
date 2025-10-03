// Scroll to login section
document.getElementById("scroll-login").addEventListener("click", () => {
  document.getElementById("login-section").scrollIntoView({ behavior: "smooth" });
});

// Handle login
document.getElementById("login-form").addEventListener("submit", function(e){
  e.preventDefault();
  const id = document.getElementById("login-id").value.trim();
  const password = document.getElementById("login-password").value.trim();

  if(id === "expendibles" && password === "team@123"){
    // Hide login section
    document.getElementById("login-section").style.display = "none";
    const readingPage = document.getElementById("reading-page");
    readingPage.style.display = "block";
    document.getElementById("user-name").textContent = id.toUpperCase();

    // Initially empty readings
    const readings = [];
    const list = document.getElementById("reading-list");
    list.innerHTML = "";

    if(readings.length === 0){
      const li = document.createElement("li");
      li.textContent = "No records available";
      list.appendChild(li);
    }

    // Polling for Raspberry Pi readings (taste only)
    function fetchPiReadings(){
      fetch("http://your-server-ip:3000/api/readings")
        .then(res => res.json())
        .then(data => {
          if(data.length > 0){
            list.innerHTML = ""; // clear "No records available"
            data.forEach(r => {
              const li = document.createElement("li");
              // Only taste display
              li.textContent = `${r.taste}`;
              list.appendChild(li);
            });
          }
        })
        .catch(err => {
          // if Pi not sending, do nothing
        });
    }

    // Poll every 10 seconds
    setInterval(fetchPiReadings, 10000);

    // Scroll to top of reading page
    readingPage.scrollIntoView({behavior:"smooth"});

  } else {
    alert("Invalid Login ID or Password!");
  }
});

// Logout button
document.getElementById("logout").addEventListener("click", () => {
  document.getElementById("reading-page").style.display = "none";
  document.getElementById("login-section").style.display = "block";
  document.getElementById("login-id").value = "";
  document.getElementById("login-password").value = "";
});

// 🌈 Gradient background: pastel shades (2x faster)
let topHue = 200;
let bottomHue = 180;
let topDir = 1;
let bottomDir = 1;

function updateGradient(){
  document.body.style.background = `linear-gradient(135deg, hsl(${topHue}, 65%, 80%), hsl(${bottomHue}, 65%, 80%))`;

  // doubled speed
  topHue += 0.225 * topDir;
  bottomHue += 0.15 * bottomDir;

  if(topHue >= 350) topDir = -1;
  if(topHue <= 120) topDir = 1;
  if(bottomHue >= 350) bottomDir = -1;
  if(bottomHue <= 120) bottomDir = 1;

  requestAnimationFrame(updateGradient);
}
updateGradient();
