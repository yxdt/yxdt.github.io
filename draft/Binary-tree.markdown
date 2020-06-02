二叉树的遍历

pre-order：前序遍历，中-左-右
in-order：中序遍历, 左-中-右
post-order：后序遍历, 左-右-中
Level-order: 从根逐层遍历，根-子-孙

javascript 实现

注意：下面的实现方式的中序、后序遍历本身对二叉树本身链接进行了修改。

```javascript
//Preorder
/**
 * Definition for a binary tree node.
 * function TreeNode(val, left, right) {
 *     this.val = (val===undefined ? 0 : val)
 *     this.left = (left===undefined ? null : left)
 *     this.right = (right===undefined ? null : right)
 * }
 */
/**
 * @param {TreeNode} root
 * @return {number[]}
 */
var preorderTraversal = function (root) {
  var ret = [];
  var stck = [];
  var item = root;
  if (!root) {
    return ret;
  }
  stck.push(root);
  while (stck.length > 0) {
    item = stck.pop();
    ret.push(item.val);
    if (item.right) {
      stck.push(item.right);
    }
    if (item.left) {
      stck.push(item.left);
    }
  }
  return ret;
};

//Inorder
/**
 * Definition for a binary tree node.
 * function TreeNode(val, left, right) {
 *     this.val = (val===undefined ? 0 : val)
 *     this.left = (left===undefined ? null : left)
 *     this.right = (right===undefined ? null : right)
 * }
 */
/**
 * @param {TreeNode} root
 * @return {number[]}
 */
var inorderTraversal = function (root) {
  //l-m-r
  var ret = [];
  var stck = [];
  var item = null;
  var tleft = null;
  if (!root) return ret;
  stck.push(root);
  while (stck.length > 0) {
    //stack is not null
    item = stck.pop();
    if (item.right) {
      stck.push(item.right);
    }
    if (item.left) {
      stck.push(item);
      stck.push(item.left);
      item.left = null;
      item.right = null;
    } else {
      ret.push(item.val);
    }
  }
  return ret;
};

//Postorder l-r-m

/**
 * Definition for a binary tree node.
 * function TreeNode(val, left, right) {
 *     this.val = (val===undefined ? 0 : val)
 *     this.left = (left===undefined ? null : left)
 *     this.right = (right===undefined ? null : right)
 * }
 */
/**
 * @param {TreeNode} root
 * @return {number[]}
 */
var postorderTraversal = function (root) {
  var ret = [];
  var stck = [];
  var item = root;
  var leaf = false;
  if (!root) {
    return ret;
  }
  stck.push(root);
  while (stck.length > 0) {
    item = stck.pop();
    leaf = item.left || item.right;
    if (!leaf) {
      ret.push(item.val);
    } else {
      stck.push(item);
    }
    if (item.right) {
      stck.push(item.right);
      item.right = null;
    }
    if (item.left) {
      stck.push(item.left);
      item.left = null;
    }
  }
  return ret;
};

/**
 * Definition for a binary tree node.
 * function TreeNode(val, left, right) {
 *     this.val = (val===undefined ? 0 : val)
 *     this.left = (left===undefined ? null : left)
 *     this.right = (right===undefined ? null : right)
 * }
 */
/**
 * @param {TreeNode} root
 * @return {number[][]}
 */
var levelOrder = function (root) {
  var ret = [];
  var stck = [];

  var item;
  var level = 0;
  if (!root) {
    return ret;
  }

  stck.push({ item: root, level: level });
  ret[level] = new Array();

  while (stck.length > 0) {
    item = stck.shift();

    if (item.level > level) {
      ret[item.level] = new Array();
      level++;
    }
    ret[item.level].push(item.item.val);

    if (item.item.left) {
      stck.push({ item: item.item.left, level: item.level + 1 });
    }
    if (item.item.right) {
      stck.push({ item: item.item.right, level: item.level + 1 });
    }
  }
  return ret;
};
```
