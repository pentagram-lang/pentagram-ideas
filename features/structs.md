# Pentagram ideas / Features / Structs

Pentagram comes with built-in structs like booleans and optional values. You can also create your own.

If your struct includes cases, the case constructor and check methods are also automatically defined as struct methods. Cases can be used as indepenent types and implement their own traits.

If any methods defined within the struct that do not use the self parameter will be static methods. Static methods can only be referenced statically, and non-static methods can only be referenced non-statically.
