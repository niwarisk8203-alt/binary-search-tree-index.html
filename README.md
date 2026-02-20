<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>BST Professional Lab | Data Structure Visualizer</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600&display=swap" rel="stylesheet">
    <style>
        body { font-family: 'Inter', sans-serif; background-color: #0f172a; color: #f8fafc; overflow: hidden; }
        .canvas-container { background-image: radial-gradient(#1e293b 1px, transparent 1px); background-size: 30px 30px; }
        .node-circle { transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1); stroke-width: 2; fill: #1e293b; stroke: #38bdf8; }
        .node-text { fill: #f8fafc; font-size: 14px; font-weight: 600; pointer-events: none; }
        .link-line { stroke: #334155; stroke-width: 2; transition: all 0.4s; }
        .highlight-node { fill: #38bdf8; stroke: #fff; }
        .sidebar { background-color: #1e293b; border-right: 1px solid #334155; }
        input::-webkit-outer-spin-button, input::-webkit-inner-spin-button { -webkit-appearance: none; margin: 0; }
    </style>
</head>
<body class="flex h-screen">

    <!-- Sidebar -->
    <aside class="sidebar w-80 p-6 flex flex-col gap-6 z-10 shadow-xl">
        <div>
            <h1 class="text-xl font-bold text-sky-400 mb-1">BST Professional</h1>
            <p class="text-xs text-slate-400 uppercase tracking-widest">Visual Laboratory</p>
        </div>

        <div class="space-y-4">
            <div>
                <label class="block text-sm text-slate-400 mb-2">Node Value</label>
                <div class="flex gap-2">
                    <input type="number" id="node-input" class="w-full bg-slate-900 border border-slate-700 rounded px-3 py-2 outline-none focus:border-sky-500 transition" placeholder="Enter number...">
                </div>
            </div>

            <div class="grid grid-cols-2 gap-2">
                <button onclick="handleInsert()" class="bg-sky-600 hover:bg-sky-500 text-white py-2 rounded text-sm font-medium transition">Insert</button>
                <button onclick="handleDelete()" class="bg-slate-700 hover:bg-rose-600 text-white py-2 rounded text-sm font-medium transition">Delete</button>
                <button onclick="handleSearch()" class="bg-slate-700 hover:bg-amber-500 text-white py-2 rounded text-sm font-medium transition col-span-2">Search Node</button>
            </div>
        </div>

        <div class="border-t border-slate-700 pt-6">
            <p class="text-sm font-semibold text-slate-300 mb-3">Traversals</p>
            <div class="text-xs space-y-3">
                <div class="bg-slate-900 p-2 rounded border border-slate-800">
                    <span class="text-sky-400 font-bold">IN-ORDER:</span>
                    <div id="inorder-res" class="mt-1 text-slate-400 break-all">-</div>
                </div>
                <div class="bg-slate-900 p-2 rounded border border-slate-800">
                    <span class="text-emerald-400 font-bold">PRE-ORDER:</span>
                    <div id="preorder-res" class="mt-1 text-slate-400 break-all">-</div>
                </div>
            </div>
        </div>

        <div class="mt-auto">
            <button onclick="resetTree()" class="w-full border border-slate-700 hover:bg-slate-800 text-slate-400 py-2 rounded text-xs transition">Clear Board</button>
        </div>
    </aside>

    <!-- Main Content -->
    <main class="flex-1 relative canvas-container overflow-hidden">
        <div id="status-msg" class="absolute top-6 left-6 text-sm text-slate-500">System Ready</div>
        <svg id="tree-svg" class="w-full h-full"></svg>
    </main>

    <script>
        class Node {
            constructor(val) {
                this.val = val;
                this.left = null;
                this.right = null;
                this.x = 0;
                this.y = 0;
            }
        }

        let root = null;
        const svg = document.getElementById('tree-svg');
        const nodeRadius = 22;
        const verticalSpacing = 80;

        function handleInsert() {
            const val = parseInt(document.getElementById('node-input').value);
            if (isNaN(val)) return;
            root = insert(root, val);
            updateView(`Node ${val} inserted.`);
        }

        function insert(node, val) {
            if (!node) return new Node(val);
            if (val < node.val) node.left = insert(node.left, val);
            else if (val > node.val) node.right = insert(node.right, val);
            return node;
        }

        function handleDelete() {
            const val = parseInt(document.getElementById('node-input').value);
            root = deleteNode(root, val);
            updateView(`Node ${val} deleted.`);
        }

        function deleteNode(node, val) {
            if (!node) return null;
            if (val < node.val) node.left = deleteNode(node.left, val);
            else if (val > node.val) node.right = deleteNode(node.right, val);
            else {
                if (!node.left) return node.right;
                if (!node.right) return node.left;
                let min = findMin(node.right);
                node.val = min.val;
                node.right = deleteNode(node.right, min.val);
            }
            return node;
        }

        function findMin(node) {
            while (node.left) node = node.left;
            return node;
        }

        function handleSearch() {
            const val = parseInt(document.getElementById('node-input').value);
            const found = search(root, val);
            updateView(found ? `Found node ${val}` : `Node ${val} not found`);
            if (found) highlightNode(val);
        }

        function search(node, val) {
            if (!node) return false;
            if (node.val === val) return true;
            return val < node.val ? search(node.left, val) : search(node.right, val);
        }

        function updateView(msg) {
            document.getElementById('status-msg').innerText = msg;
            document.getElementById('node-input').value = '';
            render();
            updateTraversals();
        }

        function render() {
            svg.innerHTML = '';
            if (!root) return;
            const width = svg.clientWidth;
            calculatePos(root, width / 2, 60, width / 4);
            drawLinks(root);
            drawNodes(root);
        }

        function calculatePos(node, x, y, sep) {
            if (!node) return;
            node.x = x;
            node.y = y;
            calculatePos(node.left, x - sep, y + verticalSpacing, sep / 1.9);
            calculatePos(node.right, x + sep, y + verticalSpacing, sep / 1.9);
        }

        function drawLinks(node) {
            if (!node) return;
            if (node.left) {
                createLine(node.x, node.y, node.left.x, node.left.y);
                drawLinks(node.left);
            }
            if (node.right) {
                createLine(node.x, node.y, node.right.x, node.right.y);
                drawLinks(node.right);
            }
        }

        function createLine(x1, y1, x2, y2) {
            const line = document.createElementNS("http://www.w3.org/2000/svg", "line");
            line.setAttribute("x1", x1); line.setAttribute("y1", y1);
            line.setAttribute("x2", x2); line.setAttribute("y2", y2);
            line.setAttribute("class", "link-line");
            svg.appendChild(line);
        }

        function drawNodes(node) {
            if (!node) return;
            const g = document.createElementNS("http://www.w3.org/2000/svg", "g");
            
            const circle = document.createElementNS("http://www.w3.org/2000/svg", "circle");
            circle.setAttribute("cx", node.x); circle.setAttribute("cy", node.y);
            circle.setAttribute("r", nodeRadius);
            circle.setAttribute("class", "node-circle");
            circle.setAttribute("id", `node-${node.val}`);

            const text = document.createElementNS("http://www.w3.org/2000/svg", "text");
            text.setAttribute("x", node.x); text.setAttribute("y", node.y + 5);
            text.setAttribute("text-anchor", "middle");
            text.setAttribute("class", "node-text");
            text.textContent = node.val;

            g.appendChild(circle);
            g.appendChild(text);
            svg.appendChild(g);

            drawNodes(node.left);
            drawNodes(node.right);
        }

        function highlightNode(val) {
            const el = document.getElementById(`node-${val}`);
            if (el) {
                el.classList.add('highlight-node');
                setTimeout(() => el.classList.remove('highlight-node'), 2000);
            }
        }

        function updateTraversals() {
            const inorder = [];
            const preorder = [];
            const traverse = (n) => {
                if (!n) return;
                preorder.push(n.val);
                traverse(n.left);
                inorder.push(n.val);
                traverse(n.right);
            };
            traverse(root);
            document.getElementById('inorder-res').innerText = inorder.join(' → ') || '-';
            document.getElementById('preorder-res').innerText = preorder.join(' → ') || '-';
        }

        function resetTree() {
            root = null;
            updateView("System Reset");
        }

        window.onresize = render;
    </script>
</body>
</html>
