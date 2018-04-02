# TO DO

* A dead entry in the vmInventory file (underlying VM was deleted / renamed) causes problems: `fvml ls [-s]` malfunctions (only VMX path is output, not display name, guest OS, and state; `-s` makes `awk` complain about missing files).