# 🛒 Auto Add Chase Offers - JavaScript Automation Script

This JavaScript script **automates clicking "Add Offer" buttons** on a webpage, ensuring that all available offers are selected **without manual effort**.  
This is designed for adding offers to your Chase credit card:
    https://secure.chase.com/web/auth/dashboard#/dashboard/merchantOffers/offerCategoriesPage?offerCategoryName=ALL

## DO NOT RUN JS ONCE AUTHENTICATED TO A BANKING PAGE UNLESS YOU KNOW WHAT IT DOES 

---

## 📌 Features
- ✅ **Automatically detects and clicks** all "Add Offer" buttons.
- ✅ **Floating UI panel** with Start/Stop buttons for easy control.
- ✅ **Tracks real-time progress** (remaining offers, estimated time left).
- ✅ **Speed settings** (**Slow, Normal, Fast**) — now defaults to **Fast mode** ⚡.
- ✅ **Mimics human-like behavior** with randomized delays (1-3 sec).
- ✅ **Stops at any time** with the Stop button.
- ✅ **Retry mechanism** ensures all offers are truly added before stopping.

---

## 🖥️ How It Works
1. **Finds all "Add Offer" buttons** using their `data-cy` attribute.
2. **Clicks the first available offer** and waits 1-3 seconds.
3. **Returns to the original page** after clicking an offer.
4. **Waits for the page to reload** before clicking the next button.
5. **Updates the floating UI panel** with real-time progress.
6. **Retries if no buttons are found**, ensuring every offer is added.
7. **Repeats the process** until all offers are added or stopped.

---

## 🛠 How to Use It
1. Open your browser and navigate to the webpage containing the offers.
2. Open the **Developer Console**:
   - **Chrome:** `Ctrl + Shift + J` (Windows) or `Cmd + Option + J` (Mac)
   - **Firefox:** `Ctrl + Shift + K` (Windows) or `Cmd + Option + K` (Mac)
   - **Edge:** `F12` → **Console**
3. **Copy & paste the script** below into the console and press **Enter**.
4. A **floating control panel** will appear on the page.
5. Click **"Start"** to begin adding offers.
6. Click **"Stop"** anytime to halt the script.
7. Adjust the **speed setting** if needed (defaults to Fast).

---

## 📝 Copy & Paste This Script
```javascript
(function () {
    const originalUrl = window.location.href;
    let isRunning = false;
    let delayTime = 1000; // Default to Fast (1 sec delay)

    // Create floating UI panel
    const panel = document.createElement("div");
    panel.innerHTML = `
        <div id="auto-offers-panel" style="
            position: fixed; bottom: 20px; right: 20px;
            width: 250px; padding: 10px; background: white;
            border: 2px solid #333; border-radius: 8px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.2);
            font-family: Arial, sans-serif; font-size: 14px;
            text-align: center; z-index: 9999;
        ">
            <h3>🛒 Auto Add Offers</h3>
            <p id="status">⏹️ Status: Stopped</p>
            <p id="progress">🔄 Offers Remaining: --</p>
            <p id="timeLeft">⏳ Est. Time Left: --</p>
            <label>⏩ Speed:</label>
            <select id="speed-control">
                <option value="3000">Slow</option>
                <option value="2000">Normal</option>
                <option value="1000" selected>Fast</option> <!-- Default set to Fast -->
            </select>
            <br><br>
            <button id="start-btn" style="padding:5px 10px; margin: 5px; cursor:pointer;">▶ Start</button>
            <button id="stop-btn" style="padding:5px 10px; margin: 5px; cursor:pointer;">⏹ Stop</button>
        </div>
    `;
    document.body.appendChild(panel);

    function clickAndReturn(attempts = 0) {
        if (!isRunning) {
            document.getElementById("status").innerText = "⏹️ Status: Stopped";
            return;
        }

        const buttons = document.querySelectorAll('[data-cy="commerce-tile-button"]');

        if (buttons.length > 0) {
            const offersLeft = buttons.length;
            const minTimeSec = ((offersLeft * (delayTime / 2)) / 1000).toFixed(0);
            const maxTimeSec = ((offersLeft * delayTime) / 1000).toFixed(0);

            document.getElementById("progress").innerText = `🔄 Offers Remaining: ${offersLeft}`;
            document.getElementById("timeLeft").innerText = `⏳ Est. Time Left: ${minTimeSec}-${maxTimeSec} sec`;
            document.getElementById("status").innerText = "✅ Status: Running...";

            console.log(`📌 Clicking an offer... (${offersLeft} remaining)`);
            console.log(`⏳ Estimated time left: ${minTimeSec}-${maxTimeSec} seconds`);

            buttons[0].click();

            let backDelay = Math.floor(Math.random() * (delayTime / 2)) + delayTime;
            setTimeout(() => {
                console.log("🔄 Returning to original page...");
                window.location.href = originalUrl;

                let reloadDelay = Math.floor(Math.random() * (delayTime / 2)) + delayTime;
                setTimeout(() => {
                    if (isRunning) {
                        console.log("⏳ Waiting before next click...");
                        clickAndReturn();
                    }
                }, reloadDelay);
            }, backDelay);
        } else {
            if (attempts < 3) {
                console.log(`⚠️ No buttons detected. Retrying (${attempts + 1}/3)...`);
                setTimeout(() => clickAndReturn(attempts + 1), 2000);
            } else {
                console.log("✅ All offers added! (or no more found)");
                document.getElementById("status").innerText = "✅ Status: Completed!";
            }
        }
    }

    document.getElementById("start-btn").addEventListener("click", () => {
        if (!isRunning) {
            isRunning = true;
            clickAndReturn();
        }
    });

    document.getElementById("stop-btn").addEventListener("click", () => {
        isRunning = false;
        document.getElementById("status").innerText = "⏹️ Status: Stopped";
        console.log("⏹️ Auto Add Offers script stopped.");
    });

    document.getElementById("speed-control").addEventListener("change", (event) => {
        delayTime = parseInt(event.target.value);
        console.log(`⏩ Speed changed to: ${event.target.options[event.target.selectedIndex].text}`);
    });

    console.log("🚀 Auto Add Offers script loaded! Use the floating panel to start.");
})();
