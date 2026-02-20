<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>BST CRUD Management System</title>
    <link href="https://fonts.googleapis.com/css2?family=Kanit:wght@300;400;600&display=swap" rel="stylesheet">
    <style>
        :root {
            --primary: #2E7D32;
            --secondary: #FB8C00;
            --danger: #D32F2F;
            --bg: #F4F7F6;
        }
        body { font-family: 'Kanit', sans-serif; background: var(--bg); margin: 0; padding: 20px; display: flex; flex-direction: column; align-items: center; }
        .app-container { background: white; padding: 30px; border-radius: 20px; box-shadow: 0 10px 30px rgba(0,0,0,0.1); width: 100%; max-width: 1000px; }
        h1 { text-align: center; color: var(--primary); margin-bottom: 25px; }
        
        /* CRUD Panel */
        .crud-controls { display: flex; justify-content: center; gap: 10px; margin-bottom: 30px; flex-wrap: wrap; }
        input { padding: 12px; border: 2px solid #DDD; border-radius: 10px; width: 120px; outline: none; font-size: 16px; }
        button { padding: 12px 20px; border: none; border-radius: 10px; cursor: pointer; color: white; font-weight: 600; transition: 0.3s; }
        
        .btn-create { background: var(--primary); } /* Create */
        .btn-delete { background: var(--danger); }  /* Delete */
        .btn-reset { background: #757575; }           /* Reset */

        /* Tree Display (Read) */
        #tree-area { position: relative; width: 100%; height: 500px; background: #FFF; border: 1px solid #EEE; border-radius: 15px; overflow: hidden; }
        .node { width: 45px; height: 45px; background: linear-gradient(145deg, #A5D6A7, #2E7D32); color: white; border-radius: 50%; display: flex; align-items: center; justify-content: center; position: absolute; font-weight: bold; z-index: 10; border: 2px solid white; box-shadow: 0 4px 8px rgba(0,0,0,0.2); transition: 0.5s; }
        svg { position: absolute; top: 0; left: 0; width: 100%; height: 100%; }
        line { stroke: #81C784; stroke-width: 3; }

        /* Status & Traversal */
        .status-panel { display: grid; grid-template-columns: repeat(3, 1fr); gap: 15px; margin-top: 25px; }
        .info-card { padding: 15px; border-radius: 12px; text-align: center; background: #F9F9F9; border-bottom: 4px solid var(--primary); }
        .info-card h4 { margin: 0 0 10px 0; color: #555; }
        .info-card p { margin: 0; font-size: 14px; color: var(--primary); font-weight: bold; }
    </style>
</head>
<body>

<div class="app-container">
    <h1>BST CRUD Management</h1>
    
    <div class="crud-controls">
        <input type="number" id="inputValue" placeholder="ระบุตัวเลข">
        <button class="btn-create" onclick="handleInsert()">➕ Create (Insert)</button>
        <button class="btn-delete" onclick="handleDelete()">🗑️ Delete (Remove)</button>
        <button class="btn-reset" onclick="handleReset()">🔄 Reset</button>
    </div>

    <div id="tree-area">
        <svg id="line-layer"></svg>
    </div>

    <div class="status-panel">
        <div class="info-card"><h4>Preorder (Read)</h4><p id="pre-out">-</p></div>
        <div class="info-card"><h4>Inorder (Read)</h4><p id="ino-out">-</p></div>
        <div class="info-card"><h4>Postorder (Read)</h4><p id="post-out">-</p></div>
    </div>
</div>

<script>
    class Node {
        constructor(v) { this.v = v; this.left = null; this.right = null; this.x = 0; this.y = 0; }
    }
    let root = null;
    const area = document.getElementById('tree-area');
    const svg = document.getElementById('line-layer');

    // CRUD: Create
    function handleInsert() {
        const v = parseInt(document.getElementById('inputValue').value);
        if (isNaN(v)) return;
        root = insert(root, v);
        document.getElementById('inputValue').value = '';
        render();
    }

    function insert(node, v) {
        if (!node) return new Node(v);
        if (v < node.v) node.left = insert(node.left, v);
        else if (v > node.v) node.right = insert(node.right, v);
        return node;
    }

    // CRUD: Delete
    function handleDelete() {
        const v = parseInt(document.getElementById('inputValue').value);
        if (isNaN(v)) return;
        root = remove(root, v);
        document.getElementById('inputValue').value = '';
        render();
    }

    function remove(node, v) {
        if (!node) return null;
        if (v < node.v) node.left = remove(node.left, v);
        else if (v > node.v) node.right = remove(node.right, v);
        else {
            if (!node.left) return node.right;
            if (!node.right) return node.left;
            let minNode = node.right;
            while (minNode.left) minNode = minNode.left;
            node.v = minNode.v;
            node.right = remove(node.right, minNode.v);
        }
        return node;
    }

    // CRUD: Read & Update UI
    function render() {
        document.querySelectorAll('.node').forEach(n => n.remove());
        svg.innerHTML = '';
        if (!root) return updateTraversals();
        calc(root, area.offsetWidth / 2, 50, area.offsetWidth / 4);
        draw(root);
        updateTraversals();
    }

    function calc(n, x, y, s) {
        if (!n) return;
        n.x = x; n.y = y;
        if (n.left) calc(n.left, x - s, y + 80, s / 2);
        if (n.right) calc(n.right, x + s, y + 80, s / 2);
    }

    function draw(n) {
        if (!n) return;
        if (n.left) drawLine(n.x, n.y, n.left.x, n.left.y);
        if (n.right) drawLine(n.x, n.y, n.right.x, n.right.y);
        const d = document.createElement('div');
        d.className = 'node'; d.innerText = n.v;
        d.style.left = (n.x - 22) + 'px'; d.style.top = (n.y - 22) + 'px';
        area.appendChild(d);
        draw(n.left); draw(n.right);
    }

    function drawLine(x1, y1, x2, y2) {
        const l = document.createElementNS("http://www.w3.org/2000/svg", "line");
        l.setAttribute("x1", x1); l.setAttribute("y1", y1);
        l.setAttribute("x2", x2); l.setAttribute("y2", y2);
        svg.appendChild(l);
    }

    function updateTraversals() {
        let pre = [], ino = [], post = [];
        function walk(n) {
            if (!n) return;
            pre.push(n.v); walk(n.left);
            ino.push(n.v); walk(n.right);
            post.push(n.v);
        }
        walk(root);
        document.getElementById('pre-out').innerText = pre.join(' → ') || '-';
        document.getElementById('ino-out').innerText = ino.join(' → ') || '-';
        document.getElementById('post-out').innerText = post.join(' → ') || '-';
    }

    function handleReset() { root = null; render(); }
</script>
</body>
</html>
