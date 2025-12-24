<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Web Biometric Attendance</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      text-align: center;
      background-color: #f0f2f5;
      margin: 0;
      padding: 0;
    }
    .logo {
      margin: 20px 0;
    }
    .employees {
      display: flex;
      flex-wrap: wrap;
      justify-content: center;
      padding: 20px;
      gap: 20px;
    }
    .employee-card {
      background: white;
      border-radius: 10px;
      box-shadow: 0 2px 8px rgba(0,0,0,0.1);
      width: 200px;
      padding: 10px;
      text-align: center;
      transition: transform 0.2s;
    }
    .employee-card:hover {
      transform: scale(1.05);
    }
    .employee-card img {
      width: 100px;
      height: 100px;
      border-radius: 50%;
      object-fit: cover;
      margin-bottom: 10px;
    }
    button {
      margin-top: 10px;
      padding: 5px 10px;
      cursor: pointer;
      border: none;
      background-color: #007bff;
      color: white;
      border-radius: 5px;
    }
    video {
      width: 100%;
      border-radius: 10px;
      margin-top: 10px;
    }
  </style>
</head>
<body>

  <!-- Logo -->
  <div class="logo">
    <img src="logo.png" alt="Company Logo" width="150">
  </div>

  <!-- Employees -->
  <h2>Employees</h2>
  <div class="employees" id="employees-container">
    <!-- Employee cards will be inserted here -->
  </div>

  <script>
    // Employee data
    const employees = [
      { id: "E001", name: "John Doe", photo: "employees/john.jpg" },
      { id: "E002", name: "Jane Smith", photo: "employees/jane.jpg" },
      { id: "E003", name: "Ali Khan", photo: "employees/ali.jpg" },
      { id: "E004", name: "Sara Lee", photo: "employees/sara.jpg" }
    ];

    const container = document.getElementById("employees-container");

    employees.forEach(emp => {
      const card = document.createElement("div");
      card.className = "employee-card";
      card.innerHTML = `
        <img src="${emp.photo}" alt="${emp.name}">
        <h4>${emp.name}</h4>
        <p>ID: ${emp.id}</p>
        <button id="attend-${emp.id}">Mark Attendance</button>
        <div id="video-container-${emp.id}" style="display:none;">
          <video autoplay></video>
          <button id="capture-${emp.id}">Capture</button>
          <p id="status-${emp.id}"></p>
        </div>
      `;
      container.appendChild(card);

      const attendBtn = document.getElementById(`attend-${emp.id}`);
      const videoContainer = document.getElementById(`video-container-${emp.id}`);
      const videoElem = videoContainer.querySelector("video");
      const captureBtn = document.getElementById(`capture-${emp.id}`);
      const status = document.getElementById(`status-${emp.id}`);
      let stream;

      attendBtn.addEventListener("click", async () => {
        videoContainer.style.display = "block";
        // start webcam
        if (!stream) {
          stream = await navigator.mediaDevices.getUserMedia({ video: true });
          videoElem.srcObject = stream;
        }
      });

      captureBtn.addEventListener("click", () => {
        const canvas = document.createElement("canvas");
        canvas.width = videoElem.videoWidth;
        canvas.height = videoElem.videoHeight;
        canvas.getContext("2d").drawImage(videoElem, 0, 0);
        // Here you can save the image to server or localStorage
        const imgData = canvas.toDataURL("image/png");
        status.textContent = "Attendance Marked!";
        // Replace employee photo with captured image
        card.querySelector("img").src = imgData;
        // Stop webcam
        stream.getTracks().forEach(track => track.stop());
        videoContainer.style.display = "none";
      });
    });
  </script>

</body>
</html>
