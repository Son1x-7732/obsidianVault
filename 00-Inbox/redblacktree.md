```
def fix_insert(self, new_node: RBNode) -> None:
    current = new_node

    while current != self.root and current.parent.red:
        parent = current.parent
        grandparent = parent.parent

        if parent == grandparent.right:
            # Right Case
            uncle = grandparent.left

            if uncle.red:
                parent.red = False
                uncle.red = False
                grandparent.red = True
                current = grandparent

            else:
                # Black uncle
                if current == parent.left:
                    current = parent
                    self.rotate_right(current)
                    parent = current.parent

                parent.red = False
                grandparent.red = True
                self.rotate_left(grandparent)

        else:
            # Left Case
            uncle = grandparent.right

            if uncle.red:
                parent.red = False
                uncle.red = False
                grandparent.red = True
                current = grandparent

            else:
                # Black uncle
                if current == parent.right:
                    current = parent
                    self.rotate_left(current)
                    parent = current.parent

                parent.red = False
                grandparent.red = True
                self.rotate_right(grandparent)

    self.root.red = False
```