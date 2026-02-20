<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>BST Visualizer - ระบบจำลองโครงสร้างต้นไม้</title>
    <style>
        body { font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; text-align: center; background-color: #f4f7f6; margin: 0; padding: 20px; }
        .controls { margin-bottom: 20px; background: white; padding: 20px; border-radius: 10px; box-shadow: 0 4px 6px rgba(0,0,0,0.1); display: inline-block; }
        input { padding: 10px; width: 60px; border: 1px solid #ddd; border-radius: 5px; outline: none; }
        button { padding: 10px 20px; cursor: pointer; background-color: #28a745; color: white; border: none; border-radius: 5px; transition: 0.3s; }
        button:hover { background-color: #218838; }
        #tree-container { position: relative; width: 100%; height: 500px; margin-top: 20px; }
        .node { position: absolute; width: 40px; height: 40px; background-color: #007bff; color: white; border-radius: 50%; display: flex; align-items: center; justify-content: center; font-weight: bold; transition: all 0.5s; z-index: 2; border: 2px solid #0056b3; }
        .line { position: absolute; background-color: #ccc; height: 2px; transform-origin: top left; z-index: 1; }
    </style>
</head>
<body>

    <h1>Binary Search Tree Visualizer</h1>
    
    <div class="controls">
        <input type="number" id="node-value" placeholder="ค่า">
        <button onclick="insertNode()">เพิ่มโหนด (Insert)</button>
        <button onclick="resetTree()" style="background-color: #dc3545;">ล้างหน้าจอ</button>
    </div>

    <div id="tree-container"></div>

    <script>
        class Node {
            constructor(value) {
                this.value = value;
                this.left = null;
                this.right = null;
                this.x = 0;
                this.y = 0;
            }
        }

        let root = null;
        const container = document.getElementById('tree-container');
        const nodeRadius = 20;
        const verticalSpacing = 70;

        function insertNode() {
            const val = parseInt(document.getElementById('node-value').value);
            if (isNaN(val)) return;
            
            if (!root) {
                root = new Node(val);
            } else {
                addNode(root, val);
            }
            document.getElementById('node-value').value = '';
            updateVisualization();
        }

        function addNode(node, val) {
            if (val < node.value) {
                if (!node.left) node.left = new Node(val);
                else addNode(node.left, val);
            } else if (val > node.value) {
                if (!node.right) node.right = new Node(val);
                else addNode(node.right, val);
            }
        }

        function updateVisualization() {
            container.innerHTML = '';
            if (!root) return;
            
            const width = container.offsetWidth;
            calculatePositions(root, width / 2, 50, width / 4);
            drawTree(root);
        }

        function calculatePositions(node, x, y, spacing) {
            if (!node) return;
            node.x = x;
            node.y = y;
            calculatePositions(node.left, x - spacing, y + verticalSpacing, spacing / 1.8);
            calculatePositions(node.right, x + spacing, y + verticalSpacing, spacing / 1.8);
        }

        function drawTree(node) {
            if (!node) return;

            // วาดเส้นเชื่อม
            if (node.left) drawLine(node.x, node.y, node.left.x, node.left.y);
            if (node.right) drawLine(node.x, node.y, node.right.x, node.right.y);

            // วาดวงกลมโหนด
            const div = document.createElement('div');
            div.className = 'node';
            div.innerText = node.value;
            div.style.left = (node.x - 20) + 'px';
            div.style.top = (node.y - 20) + 'px';
            container.appendChild(div);

            drawTree(node.left);
            drawTree(node.right);
        }

        function drawLine(x1, y1, x2, y2) {
            const line = document.createElement('div');
            line.className = 'line';
            const length = Math.sqrt(Math.pow(x2 - x1, 2) + Math.pow(y2 - y1, 2));
            const angle = Math.atan2(y2 - y1, x2 - x1) * 180 / Math.PI;
            
            line.style.width = length + 'px';
            line.style.left = x1 + 'px';
            line.style.top = y1 + 'px';
            line.style.transform = `rotate(${angle}deg)`;
            container.appendChild(line);
        }

        function resetTree() {
            root = null;
            updateVisualization();
        }

        window.onresize = updateVisualization;
    </script>
</body>
</html>
