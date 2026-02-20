# Binary Search Tree Implementation

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Binary Search Tree</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f4f4f4;
            margin: 0;
            padding: 20px;
        }
        h1 {
            color: #333;
        }
        #tree {
            margin-top: 20px;
        }
    </style>
</head>
<body>
    <h1>Binary Search Tree Visualization</h1>
    <div id="tree"></div>
    <script>
        class Node {
            constructor(data) {
                this.data = data;
                this.left = null;
                this.right = null;
            }
        }

        class BinarySearchTree {
            constructor() {
                this.root = null;
            }

            insert(data) {
                const newNode = new Node(data);
                if (this.root === null) {
                    this.root = newNode;
                } else {
                    this.insertNode(this.root, newNode);
                }
            }

            insertNode(node, newNode) {
                if (newNode.data < node.data) {
                    if (node.left === null) {
                        node.left = newNode;
                    } else {
                        this.insertNode(node.left, newNode);
                    }
                } else {
                    if (node.right === null) {
                        node.right = newNode;
                    } else {
                        this.insertNode(node.right, newNode);
                    }
                }
            }

            display(node = this.root) {
                if (node !== null) {
                    this.display(node.left);
                    console.log(node.data);
                    this.display(node.right);
                }
            }
        }

        const bst = new BinarySearchTree();
        bst.insert(10);
        bst.insert(5);
        bst.insert(15);
        bst.insert(3);
        bst.insert(7);
        bst.insert(12);
        bst.insert(18);

        bst.display();
    </script>
</body>
</html>
