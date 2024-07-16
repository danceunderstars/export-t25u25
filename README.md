# export-t25u25

JS code to run on your browser (chromium-based and firefox). Simply open the developer tools while on the ballot page and run the code on the console tab.

## Instructions

Certify that your rankings are finalized. 

While on your browser, in your submission ballot page (survey), enter `Ctrl + Shift + I` (Windows), `Command + Option + i` (Mac). This command should toggle the developer tools (it is usually displayed on the bottom or on the right of your browser page). Then on the top navigation menu bar on the developer tools, go to "Console". Paste the following code:

```javascript
let l = [...document.querySelectorAll('.tally-block-ranking-option')]

let ranks = l.map(el => el.querySelector('.tally-rank-badge').innerHTML)

let players = l.map(el => [...el.getElementsByTagName('label')].map(el => el.textContent)).map(el => el[0])

let combined = ranks.map((r, i) => ({ rank: r, player: players[i] }));

function arrayToTextTable(data) {
    const headers = Object.keys(data[0]);
    const headerRow = headers.join('\t'); // Use tab for separation
    const rows = data.map(row => headers.map(header => {
        // Strip any HTML if present
        let cellValue = row[header];
        if (typeof cellValue === 'string') {
            // Create a temporary element to strip HTML tags
            let tempElement = document.createElement('div');
            tempElement.innerHTML = cellValue;
            cellValue = tempElement.textContent || tempElement.innerText || '';
        }
        return cellValue;
    }).join('\t'));
    return [headerRow, ...rows].join('\n');
}

let textTable = arrayToTextTable(combined);
console.log(textTable);

function downloadTextFile(text, filename) {
    let textFile = new Blob([text], { type: 'text/plain' });
    let downloadLink = document.createElement("a");

    downloadLink.download = filename;
    downloadLink.href = window.URL.createObjectURL(textFile);
    downloadLink.style.display = "none";

    document.body.appendChild(downloadLink);
    downloadLink.click();
    document.body.removeChild(downloadLink);
}

downloadTextFile(textTable, 'My T25U25 rankings.txt');
```

If you are on chromium based browsers, a window should pop up to save your file in the directory of your choice. For Firefox users, the download should be automatic, simply open the folder in which the file was downloaded to.
