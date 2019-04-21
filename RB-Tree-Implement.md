# 红黑树的实现
``` java
package com.raycolor.practice.blackredtree;

import com.sun.org.apache.regexp.internal.RE;

import java.util.Comparator;

public class BuildBlackRedTree {
    private static final int BLACK = 1;
    private static final int RED = 2;

    private static Node watchNode = new Node().setColor(BLACK);

    public static <T> Node<T> createNode(T value) {
        Node<T> node = new Node<T>();
        node.value = value;
        node.color = RED;
        node.leftChild = watchNode;
        node.rightChild = watchNode;
        node.preNode = watchNode;
        return node;
    }

    public static <T> Node<T> findNode(T value, Comparator<T> comparator) {
        Node<T> root = watchNode.leftChild;
        if (root == null) {
            System.out.println("The tree is null");
            return null;
        }
        while (root != watchNode) {
            T currentValue = root.value;
            int relation = comparator.compare(currentValue, value);
            if (relation < 0) {
                // 当前节点小于查找值往右走
                root = root.rightChild;
            } else if (relation > 0) {
                // 当前节点大于查找值往左走
                root = root.leftChild;
            } else {
                // 相等返回
                return root;
            }
        }
        System.out.println("Not fined the node");
        return null;
    }

    /**
     * 新增节点
     * @param node 要新增的节点
     * @param comparator 比较器
     * @param <T> 节点值的类型
     * @return
     */
    public static <T> Node<T> insertNode(Node<T> node, Comparator<T> comparator){
        // 从卫兵节点中获取出根节点
        Node<T> root = watchNode.leftChild;
        if (root == null) {
            watchNode.leftChild = node;
            node.color = BLACK;
            return node;
        }
        while (true) {
            T currentValue = root.value;
            T insertValue = node.value;
            int relation = comparator.compare(currentValue, insertValue);
            if (relation > 0) {
                if (root.leftChild == watchNode) {
                    root.leftChild = node;
                    node.preNode = root;
                    break;
                } else {
                    root = root.leftChild;
                    continue;
                }
            }
            if (relation < 0) {
                if (root.rightChild == watchNode) {
                    root.rightChild = node;
                    node.preNode = root;
                    break;
                } else {
                    root = root.rightChild;
                    continue;
                }
            }
            if (relation == 0) {
                // 插入重复值直接返回
                return watchNode;
            }
        }

        return insertAdjustTree(node);
    }

    /**
     * 节点删除
     * @param value
     * @param comparator
     * @param <T>
     */
    public static <T> void deleteNode(T value, Comparator<T> comparator) {
        Node<T> node = findNode(value, comparator);
        int originalColor = node.color;
        Node<T> y, x;
        if (node.leftChild == watchNode) {
            x = node.rightChild;
            rbTransplant(node, node.rightChild);
        } else if (node.rightChild == watchNode) {
            x = node.leftChild;
            rbTransplant(node, node.leftChild);
        } else {
            y = treeMinimum(node.rightChild);
            originalColor = y.color;
            x = y.rightChild;
            if (y.preNode == node) {
                x.preNode = y;
            } else {
                rbTransplant(y,y.rightChild);
                y.rightChild = node.rightChild;
                y.rightChild.preNode = y;
            }
            rbTransplant(node,y);
            y.leftChild = node.leftChild;
            y.leftChild.preNode = y;
            y.color = node.color;
        }
        if (originalColor == BLACK) {
            deleteAdjustTree(x);
        }
    }

    /**
     * 删除后的节点调整
     * @param node
     * @param <T>
     */
    private static <T> void deleteAdjustTree(Node<T> node) {
        while (node != watchNode.leftChild && node.color == BLACK) {
            Node<T> father = node.preNode;
            if (node == father.leftChild) {
                // 我是左孩子
                // 兄弟是右
                Node<T> brother  = father.rightChild;
                if (brother.color == RED) {
                    // 情况 1
                    father.color = RED;
                    brother.color = BLACK;
                    leftRotate(father);
                    brother = father.rightChild;
                }
                if (brother.leftChild.color == BLACK && brother.rightChild.color == BLACK) {
                    // 情况 2
                    brother.color = RED;
                    node = father;
                } else {
                    if (brother.rightChild.color == BLACK) {
                        // 情况 3
                        brother.leftChild.color = BLACK;
                        brother.color = RED;
                        rightRotate(brother);
                        brother = father.rightChild;
                    }
                    // 情况 4
                    brother.color = father.color;
                    father.color = BLACK;
                    brother.rightChild.color = BLACK;
                    leftRotate(father);
                    node = watchNode.leftChild;
                }
            } else {
                // 我是右孩子
                // 兄弟是左
                Node<T> brother  = father.leftChild;
                if (brother.color == RED) {
                    // 情况 1
                    father.color = RED;
                    brother.color = BLACK;
                    rightRotate(father);
                    brother = father.leftChild;
                }
                if (brother.leftChild.color == BLACK && brother.rightChild.color == BLACK) {
                    // 情况 2
                    brother.color = RED;
                    node = father;
                } else {
                    if (brother.leftChild.color == BLACK) {
                        // 情况 3
                        brother.color = RED;
                        brother.rightChild.color = BLACK;
                        leftRotate(brother);
                        brother = father.leftChild;
                    }
                    // 情况 4
                    brother.color = father.color;
                    father.color = BLACK;
                    brother.leftChild.color = BLACK;
                    rightRotate(father);
                    node = watchNode.leftChild;
                }
            }
        }
        node.color = BLACK;
    }

    /**
     * 返回最小节点
     * @param node 节点
     * @param <T> 节点值的类型
     * @return
     */
    private static <T> Node<T> treeMinimum(Node<T> node) {
        Node<T> preNode = watchNode;
        while (node != watchNode) {
            preNode = node;
            node = node.leftChild;
        }
        return preNode;
    }

    /**
     * 中序遍历
     * @param node
     * @param <T>
     */
    public static <T> void midThrough(Node<T> node) {
        if (node == watchNode) {
            return;
        }
        midThrough(node.leftChild);
        System.out.printf(node.value + " ");
        midThrough(node.rightChild);
    }

    /**
     * 先序遍历
     * @param node
     * @param <T>
     */
    public static <T> void firstThrough(Node<T> node) {
        if (node == watchNode) {
            return;
        }
        System.out.printf(node.value + " ");
        firstThrough(node.leftChild);
        firstThrough(node.rightChild);
    }

    /**
     * 删除时子节点于父节点要进行值的交换
     * @param replayedNode 需要删除的节点
     * @param childNode  需要删除的节点的孩子节点
     * @param <T> 节点值的类型
     */
    private static <T> void rbTransplant(Node<T> replayedNode, Node<T> childNode) {
        Node<T> father = replayedNode.preNode;
        if (father == watchNode) {
            watchNode.leftChild = childNode;
        } else if (father.leftChild == replayedNode) {
            father.leftChild = childNode;
        } else {
            father.rightChild = childNode;
        }
        childNode.preNode = replayedNode.preNode;
    }

    /**
     * 采用自下而上的调节
     * 确保树的特性不会被破坏
     * @param node 当前插入的节点
     * @param <T> 节点值的类型
     * @return
     */
    private static <T> Node<T> insertAdjustTree(Node<T> node) {
        while (node.preNode.color != BLACK) {
            Node<T> grandpa = node.preNode.preNode;
            if (grandpa.leftChild == node.preNode) {
                Node<T> uncle  = grandpa.rightChild;
                if (uncle.color == RED) {
                    // 情况 1
                    node.preNode.color = BLACK;
                    uncle.color = BLACK;
                    grandpa.color = RED;
                    node = grandpa;
                } else {
                    if (node.preNode.rightChild == node) {
                        // 情况 2
                        node = node.preNode;
                        leftRotate(node);
                    }
                    // 情况 3
                    grandpa.color = RED;
                    node.preNode.color = BLACK;
                    rightRotate(grandpa);
                }
            } else {
                Node<T> uncle  = grandpa.leftChild;
                if (uncle.color == RED) {
                    // 情况 1
                    node.preNode.color = BLACK;
                    uncle.color = BLACK;
                    grandpa.color = RED;
                    node = grandpa;
                } else {
                    if (node.preNode.leftChild == node) {
                        node = node.preNode;
                        rightRotate(node);
                    }
                    grandpa.color = RED;
                    node.preNode.color = BLACK;
                    leftRotate(grandpa);
                }
            }
        }
        watchNode.leftChild.color = BLACK;
        return null;
    }

    /**
     * 右旋转
     * @param node 旋转点
     * @param <T> 节点值的类型
     */
    private static <T> void rightRotate(Node<T> node) {
        Node<T> childRight = node.leftChild.rightChild;
        Node<T> child = node.leftChild;

        node.leftChild = childRight;
        childRight.preNode = node;
        setPreNodeLocal(node, child);
        child.rightChild = node;
        node.preNode = child;
    }

    /**
     * 左旋转
     * @param node 旋转点
     * @param <T> 节点值的类型
     */
    private static <T> void leftRotate(Node<T> node) {
        Node<T> childLeft = node.rightChild.leftChild;
        Node<T> child = node.rightChild;

        node.rightChild = childLeft;
        childLeft.preNode = node;
        setPreNodeLocal(node, child);
        child.leftChild = node;
        node.preNode = child;
    }

    /**
     * 旋转时产生的节点值的交换
     * @param node
     * @param child
     * @param <T>
     */
    private static <T> void setPreNodeLocal(Node<T> node, Node<T> child) {
        child.preNode = node.preNode;
        if (node.preNode.leftChild == node) {
            node.preNode.leftChild = child;
        } else {
            node.preNode.rightChild = child;
        }
    }

    public static void main(String[] arg) {
        int[] arrays = {41,38,31,12,19,8};
        IntegerComparator integerComparator = new IntegerComparator();
        for (int value : arrays) {
            Node<Integer> node = createNode(value);
            insertNode(node,integerComparator);
        }

        deleteNode(19, integerComparator);

        System.out.println("mid");
        midThrough(watchNode.leftChild);
        System.out.println("\n first");
        firstThrough(watchNode.leftChild);
    }

    /**
     * 对象比较器
     */
    private static class IntegerComparator implements Comparator<Integer> {

        public int compare(Integer o1, Integer o2) {
            return o1.compareTo(o2);
        }
    }

    /**
     * 树的节点
     */
    private static class Node<T>{
        /**
         * 节点中的值
         */
        public T value;

        /**
         * 节点颜色
         */
        public int color ;

        /**
         * 父节点
         */
        public Node<T> preNode;

        /**
         * 左子节点
         */
        public Node<T> leftChild;

        /**
         * 右子节点
         */
        public Node<T> rightChild;


        public Node<T> setColor(int color) {
            this.color = color;
            return this;
        }

    }

}

```