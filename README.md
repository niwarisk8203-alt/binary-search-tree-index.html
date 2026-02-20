<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>BST Aurora | Ultimate Visualizer</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap" rel="stylesheet">
    <style>
        :root {
            --primary: #8b5cf6;
            --secondary: #ec4899;
            --bg: #030712;
        }

        body {
            font-family: 'Plus Jakarta Sans', sans-serif;
            background: var(--bg);
            color: white;
            overflow: hidden;
            height: 100vh;
        }

        /* Animated Background */
        .bg-glow {
            position: fixed;
            top: 0; left: 0; width: 100%; height: 100%;
            background: radial-gradient(circle at 50% 50%, #1e1b4b 0%, #030712 100%);
            z-index: -1;
        }

        .blob {
            position: absolute;
            width: 500px; height: 500px;
            background: linear-gradient(135deg, rgba(139, 92, 246, 0.3), rgba(236, 72, 153, 0.3));
            filter: blur(80px);
            border-radius: 50%;
            animation: move 20s infinite alternate;
            z-index: -1;
        }

        @keyframes move {
            from { transform: translate(-10%, -10%); }
            to { transform: translate(20%, 20%); }
        }

        /* Glassmorphism */
        .glass {
            background: rgba(255, 255, 255, 0.03);
            backdrop-filter: blur(12px);
            border: 1px solid rgba(255, 255, 255, 0.1);
            border-radius: 24px;
        }

        .btn-premium {
            background: linear-gradient(135deg, var(--primary), var(--secondary));
            transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
            box-shadow: 0 4px 15px rgba(139, 92, 246, 0.4);
        }

        .btn-premium:hover {
            transform: translateY(-2px);
            box-shadow: 0 8px 25px rgba(139, 92, 246, 0.6);
            filter: brightness(1.1);
        }

        input {
            background: rgba(255, 255, 255, 0.05) !important;
            border: 1px solid rgba(255, 255, 255, 0.1) !important;
            color: white !important;
            transition: all 0.3s;
        }

        input:focus {
            border-color: var(--primary) !important;
            box-shadow: 0 0 0 2px rgba(139, 92, 246, 0.3);
        }

        /* Tree Styles */
        .node-circle {
            fill: #1e1b4b;
            stroke: url(#grad1);
            stroke-width: 3;
            filter: drop-shadow(0 0 8px rgba(139, 92, 246, 0.5));
            transition: all 0.5s cubic-bezier(0.175, 0.885, 0.32, 1.275);
        }

        .node-group:hover .node-circle {
            stroke: #fff;
            filter: drop-shadow(0 0 15px rgba(255, 255, 255, 0.8));
        }

        .link-path {
            fill: none;
            stroke: rgba(255, 255, 255, 0.1);
            stroke-width: 2;
            stroke-dasharray: 1000;
            stroke-dashoffset: 1000;
            animation: draw 1.5s ease forwards;
        }

        @keyframes draw { to { stroke-dashoffset: 0; } }

        .highlight {
            animation: pulse 1s infinite alternate;
        }

        @keyframes pulse {
            from { filter: drop-shadow(0 0 5px #fff); }
            to { filter: drop-shadow(0 0 20px var(--primary)); }
        }
    </style>
</head>
<body>

    <div class="bg-glow"></div>
    <div class="blob"></div>

    <div class="flex h-screen p-6 gap-6">
        <!-- Control Panel -->
        <aside class="w-80 glass p-8 flex flex-col gap-8 shadow-2xl">
            <div>
                <h1 class="text-3xl font-extrabold tracking-tighter bg-clip-text text-transparent bg-gradient-to-r from-violet-400 to-pink-400">
                    AURORA BST
                </h1>
                <p class="text-[10px] uppercase tracking-[0.3em] text-slate-400 font-semibold mt-1">Data Visual Experience</p>
            </div>

            <div class="space-y-6">
                <div class="relative">
                    <input type="number" id="valInput" placeholder="Value" class="w-full px-5 py-4 rounded-2xl outline-none text-lg font-semibold">
                </div>
                
                <div class="grid grid-cols-2 gap-3">
                    <button onclick="onInsert()" class="btn-premium py-4 rounded-2xl font-bold text-sm uppercase tracking-wider">Insert</button>
                    <button onclick="onDelete()" class="bg-white/5 hover:bg-white/10 py-4 rounded-2xl font-bold text-sm uppercase tracking-wider border border-white/10">Delete</button>
                </div>
                
                <button onclick="onSearch()" class="w-full bg-indigo-500/20 hover:bg-indigo-500/30 text-indigo-300 py-4 rounded-2xl font-bold border border-indigo-500/30 transition-all">
                    Search Node
                </button>
            </div>

            <div class="space-y-4 mt-4">
                <div class="glass p-4 rounded-2xl">
                    <p class="text-[10px] text-slate-500 font-bold uppercase mb-2">In-Order Path</p>
                    <div id="inorder" class="text-sm font-medium text-slate-300 truncate">-</div>
                </div>
            </div>

            <div class="mt-auto">
                <button onclick="randomTree()" class="w-full text-slate-400 hover:text-white text-xs font-semibold py-2 transition">Auto-Generate Nodes</button>
                <button onclick="resetTree()" class="w-full text-rose-400/50 hover:text-rose-400 text-xs font-semibold py-2 transition">Clear Canvas</button>
            </div>
        </aside>

        <!-- Visualization Area -->
        <main class="flex-1 glass relative overflow-hidden shadow-2xl">
            <div id="status" class="absolute top-8 left-8 text-xs font-bold text-white/30 tracking-widest uppercase italic">System: Standby</div>
            <svg id="canvas" class="w-full h-full">
                <defs>
                    <linearGradient id="grad1" x1="0%" y1="0%" x2="100%" y2="100%">
                        <stop offset="0%" style="stop-color:#8b5cf6;stop-opacity:1" />
                        <stop offset="100%" style="stop-color:#ec4899;stop-opacity:1" />
                    </linearGradient>
                </defs>
            </svg>
        </main>
    </div>

    <script>
        class Node {
            constructor(v) { this.v = v; this.l = null; this.r = null; this.x = 0; this.y = 0; }
        }

        let root = null;
        const svg = document.getElementById('canvas');

        function onInsert() {
            const v = parseInt(document.getElementById('valInput').value);
            if (isNaN(v)) return;
            root = insert(root, v);
            render("Node Added Successfully");
        }

        function insert(n, v) {
            if (!n) return new Node(v);
            if (v < n.v) n.l = insert(n.l, v);
            else if (v > n.v) n.r = insert(n.r, v);
            return n;
        }

        function onDelete() {
            const v = parseInt(document.getElementById('valInput').value);
            root = remove(root, v);
            render("Node Removed");
        }

        function remove(n, v) {
            if (!n) return null;
            if (v < n.v) n.l = remove(n.l, v);
            else if (v > n.v) n.r = remove(n.r, v);
            else {
                if (!n.l) return n.r;
                if (!n.r) return n.l;
                let m = n.r; while (m.l) m = m.l;
                n.v = m.v; n.r = remove(n.r, m.v);
            }
            return n;
        }

        function onSearch() {
            const v = parseInt(document.getElementById('valInput').value);
            const found = search(root, v);
            if (found) {
                const el = document.getElementById(`node-${v}`);
                el.classList.add('highlight');
                setTimeout(() => el.classList.remove('highlight'), 2000);
                updateStatus(`Found Node ${v}`);
            } else {
                updateStatus(`Node ${v} Not Found`);
            }
        }

        function search(n, v) {
            if (!n) return false;
            if (n.v === v) return true;
            return v < n.v ? search(n.l, v) : search(n.r, v);
        }

        function render(msg) {
            svg.innerHTML = svg.querySelector('defs').outerHTML;
            if (!root) return;
            updateStatus(msg || "Updated");
            const w = svg.clientWidth;
            pos(root, w / 2, 80, w / 4);
            drawLinks(root);
            drawNodes(root);
            updateTraversals();
            document.getElementById('valInput').value = '';
        }

        function pos(n, x, y, s) {
            if (!n) return;
            n.x = x; n.y = y;
            pos(n.l, x - s, y + 100, s / 2);
            pos(n.r, x + s, y + 100, s / 2);
        }

        function drawLinks(n) {
            if (!n) return;
            if (n.l) { line(n.x, n.y, n.l.x, n.l.y); drawLinks(n.l); }
            if (n.r) { line(n.x, n.y, n.r.x, n.r.y); drawLinks(n.r); }
        }

        function line(x1, y1, x2, y2) {
            const l = document.createElementNS("http://www.w3.org/2000/svg", "path");
            const d = `M${x1},${y1} C${x1},${(y1+y2)/2} ${x2},${(y1+y2)/2} ${x2},${y2}`;
            l.setAttribute("d", d);
            l.setAttribute("class", "link-path");
            svg.appendChild(l);
        }

        function drawNodes(n) {
            if (!n) return;
            const g = document.createElementNS("http://www.w3.org/2000/svg", "g");
            g.setAttribute("class", "node-group");
            g.setAttribute("id", `node-${n.v}`);

            const c = document.createElementNS("http://www.w3.org/2000/svg", "circle");
            c.setAttribute("cx", n.x); c.setAttribute("cy", n.y);
            c.setAttribute("r", 25);
            c.setAttribute("class", "node-circle");

            const t = document.createElementNS("http://www.w3.org/2000/svg", "text");
            t.setAttribute("x", n.x); t.setAttribute("y", n.y + 6);
            t.setAttribute("text-anchor", "middle");
            t.setAttribute("fill", "white");
            t.setAttribute("style", "font-weight: 800; font-size: 14px;");
            t.textContent = n.v;

            g.appendChild(c); g.appendChild(t);
            svg.appendChild(g);
            drawNodes(n.l); drawNodes(n.r);
        }

        function updateStatus(m) {
            document.getElementById('status').innerText = `System: ${m}`;
        }

        function updateTraversals() {
            const res = [];
            const walk = (n) => { if (n) { walk(n.l); res.push(n.v); walk(n.r); } };
            walk(root);
            document.getElementById('inorder').innerText = res.join(' → ') || '-';
        }

        function randomTree() {
            resetTree();
            for(let i=0; i<7; i++) {
                root = insert(root, Math.floor(Math.random() * 99) + 1);
            }
            render("Randomized Tree Generated");
        }

        function resetTree() { root = null; render("Canvas Cleared"); }

        window.onresize = render;
    </script>
</body>
</html>
