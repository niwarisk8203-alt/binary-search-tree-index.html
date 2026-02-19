# binary-search-tree
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>BST Visualizer</title>
    <style>
        body { font-family: 'Segoe UI', sans-serif; background: #f0f2f5; display: flex; flex-direction: column; align-items: center; margin: 0; }
        .controls { background: white; padding: 20px; width: 100%; display: flex; justify-content: center; gap: 10px; box-shadow: 0 2px 10px rgba(0,0,0,0.1); z-index: 10; }
        input { padding: 10px; border: 1px solid #ddd; border-radius: 4px; outline: none; }
        button { padding: 10px 20px; border: none; border-radius: 4px; cursor: pointer; background: #007bff; color: white; font-weight: bold; }
        button:hover { background: #0056b3; }
        button.clear { background: #dc3545; }
        
        #tree-container { position: relative; width: 100%; height: 80vh; overflow: auto; padding-top: 50px; }
        .node {
            position: absolute;
            width: 40px;
            height: 40px;
            background: #2ecc71;
            color: white;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: bold;
            transition: all 0.5s ease;
            box-shadow: 0 4px 6px rgba(0,0,0,0.1);
            z-index: 2;
        }
        svg { position: absolute; top: 0; left: 0; width: 100%; height: 100%; z-index: 1; pointer-events: none; }
    </style>
</head>
<body>

<div class="controls">
    <input type="number" id="node-input" placeholder="Enter a number">
    <button onclick="handleInsert()">Insert Node</button>
    <button class="clear" onclick="resetTree()">Clear Tree</button>
</div>

<div id="tree-container">
    <svg id="svg-canvas"></svg>
    <div id="nodes-layer"></div>
</div>

<script>
    class Node {
        constructor(value, x, y, level) {
            this.value = value;
            this.left = null;
            this.right = null;
            this.x = x;
            this.y = y;
            this.level = level;
        }
    }

    let root = null;
    const container = document.getElementById('tree-container');
    const nodesLayer = document.getElementById('nodes-layer');
    const svgCanvas = document.getElementById('svg-canvas');
    const input = document.getElementById('node-input');

    const VERTICAL_SPACING = 80;

    function handleInsert() {
        const val = parseInt(input.value);
        if (isNaN(val)) return;
        
        insert(val);
        input.value = '';
        render();
    }

    function insert(value) {
        if (!root) {
            root = new Node(value, window.innerWidth / 2, 50, 0);
            return;
        }
        recursiveInsert(root, value, window.innerWidth / 2, 50, 0, window.innerWidth / 4);
    }

    function recursiveInsert(node, value, x, y, level, offset) {
        if (value < node.value) {
            if (node.left) {
                recursiveInsert(node.left, value, node.left.x, node.left.y, level + 1, offset / 1.8);
            } else {
                node.left = new Node(value, x - offset, y + VERTICAL_SPACING, level + 1);
            }
        } else if (value > node.value) {
            if (node.right) {
                recursiveInsert(node.right, value, node.right.x, node.right.y, level + 1, offset / 1.8);
            } else {
                node.right = new Node(value, x + offset, y + VERTICAL_SPACING, level + 1);
            }
        }
    }

    function render() {
        nodesLayer.innerHTML = '';
        svgCanvas.innerHTML = '';
        if (root) {
            drawNode(root);
        }
    }

    function drawNode(node) {
        // Create Visual Circle Node
        const div = document.createElement('div');
        div.className = 'node';
        div.innerText = node.value;
        div.style.left = `${node.x - 20}px`;
        div.style.top = `${node.y - 20}px`;
        nodesLayer.appendChild(div);

        // Draw lines to children
        if (node.left) {
            drawLine(node.x, node.y, node.left.x, node.left.y);
            drawNode(node.left);
        }
        if (node.right) {
            drawLine(node.x, node.y, node.right.x, node.right.y);
            drawNode(node.right);
        }
    }

    function drawLine(x1, y1, x2, y2) {
        const line = document.createElementNS("http://www.w3.org/2000/svg", "line");
        line.setAttribute("x1", x1);
        line.setAttribute("y1", y1);
        line.setAttribute("x2", x2);
        line.setAttribute("y2", y2);
        line.setAttribute("stroke", "#bdc3c7");
        line.setAttribute("stroke-width", "2");
        svgCanvas.appendChild(line);
    }

    function resetTree() {
        root = null;
        render();
    }

    // Allow "Enter" key to insert
    input.addEventListener("keyup", (event) => {
        if (event.key === "Enter") handleInsert();
    });
</script>

</body>
</html>
