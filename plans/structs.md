# Pentagram ideas / Plans / Structs

Pentagram comes with built-in structs like booleans and optional values. You can also create your own.

If your struct includes cases, the case constructor and check methods are also automatically defined as struct methods.

If any methods defined within the struct that do not use the self parameter will be static methods. Static methods can only be referenced statically, and non-static methods can only be referenced non-statically.
