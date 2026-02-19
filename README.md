# binary-search-tree
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>BST Canvas Pro</title>
    <style>
        :root {
            --bg: #0f172a;
            --accent: #38bdf8;
            --node-bg: #1e293b;
            --text: #f8fafc;
            --highlight: #fbbf24;
        }

        body {
            margin: 0;
            background-color: var(--bg);
            color: var(--text);
            font-family: 'Inter', system-ui, sans-serif;
            overflow: hidden;
            display: flex;
            flex-direction: column;
            height: 100vh;
        }

        .toolbar {
            padding: 1rem;
            background: rgba(30, 41, 59, 0.8);
            backdrop-filter: blur(10px);
            display: flex;
            gap: 1rem;
            border-bottom: 1px solid #334155;
            justify-content: center;
        }

        input {
            background: #0f172a;
            border: 1px solid #334155;
            color: white;
            padding: 0.5rem 1rem;
            border-radius: 6px;
            outline: none;
        }

        button {
            background: var(--accent);
            color: var(--bg);
            border: none;
            padding: 0.5rem 1.2rem;
            border-radius: 6px;
            font-weight: 600;
            cursor: pointer;
            transition: opacity 0.2s;
        }

        button:hover { opacity: 0.8; }
        button.secondary { background: #64748b; color: white; }

        #canvas-container {
            flex-grow: 1;
            position: relative;
        }

        canvas { width: 100%; height: 100%; }

        .legend {
            position: absolute;
            bottom: 20px;
            left: 20px;
            font-size: 0.8rem;
            color: #94a3b8;
        }
    </style>
</head>
<body>

<div class="toolbar">
    <input type="number" id="valInput" placeholder="Enter Value">
    <button onclick="action('insert')">Insert</button>
    <button class="secondary" onclick="action('search')">Search</button>
    <button class="secondary" style="background:#ef4444" onclick="action('clear')">Clear</button>
</div>

<div id="canvas-container">
    <canvas id="treeCanvas"></canvas>
    <div class="legend">
        ● Smaller values → Left <br>
        ● Larger values → Right
    </div>
</div>

<script>
    const canvas = document.getElementById('treeCanvas');
    const ctx = canvas.getContext('2d');
    const input = document.getElementById('valInput');

    class Node {
        constructor(val) {
            this.val = val;
            this.left = null;
            this.right = null;
            this.highlight = false;
        }
    }

    let root = null;

    function resize() {
        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;
        draw();
    }

    window.addEventListener('resize', resize);
    resize();

    function action(type) {
        const val = parseInt(input.value);
        if (type === 'clear') {
            root = null;
        } else if (!isNaN(val)) {
            if (type === 'insert') {
                root = insert(root, val);
            } else if (type === 'search') {
                resetHighlights(root);
                search(root, val);
            }
        }
        input.value = '';
        draw();
    }

    function insert(node, val) {
        if (!node) return new Node(val);
        if (val < node.val) node.left = insert(node.left, val);
        else if (val > node.val) node.right = insert(node.right, val);
        return node;
    }

    function search(node, val) {
        if (!node) return;
        node.highlight = true;
        if (val < node.val) search(node.left, val);
        else if (val > node.val) search(node.right, val);
    }

    function resetHighlights(node) {
        if (!node) return;
        node.highlight = false;
        resetHighlights(node.left);
        resetHighlights(node.right);
    }

    function draw() {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        if (root) {
            drawNode(root, canvas.width / 2, 60, canvas.width / 4);
        }
    }

    function drawNode(node, x, y, offset) {
        const radius = 22;

        // Draw connections first (so they go behind circles)
        if (node.left) {
            ctx.beginPath();
            ctx.moveTo(x, y);
            ctx.lineTo(x - offset, y + 80);
            ctx.strokeStyle = '#334155';
            ctx.lineWidth = 2;
            ctx.stroke();
            drawNode(node.left, x - offset, y + 80, offset / 1.9);
        }

        if (node.right) {
            ctx.beginPath();
            ctx.moveTo(x, y);
            ctx.lineTo(x + offset, y + 80);
            ctx.strokeStyle = '#334155';
            ctx.lineWidth = 2;
            ctx.stroke();
            drawNode(node.right, x + offset, y + 80, offset / 1.9);
        }

        // Draw Circle
        ctx.beginPath();
        ctx.arc(x, y, radius, 0, Math.PI * 2);
        ctx.fillStyle = node.highlight ? '#fbbf24' : '#1e293b';
        ctx.fill();
        ctx.strokeStyle = node.highlight ? '#fff' : '#38bdf8';
        ctx.lineWidth = 3;
        ctx.stroke();

        // Draw Value
        ctx.fillStyle = node.highlight ? '#000' : '#fff';
        ctx.font = 'bold 14px Inter';
        ctx.textAlign = 'center';
        ctx.textBaseline = 'middle';
        ctx.fillText(node.val, x, y);
    }
</script>

</body>
</html>
