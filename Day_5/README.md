# Optimization in Synthesis
---
In the previous module, we saw that synthesis of the HDL code is done very differently. So as designer it's important to know what HDL code corresponds with which synthesis, because our end motive is to have a actual design which can be fabricated and works according to our speicification. In this module we will work on that.
## Synthesis of "If-else" & "Case" Statement
### If-Else Statement
When **if-else** is used in HDL code, we all know the following order will be follow first **if** condition will be match then **else if** condition and at last **end** part will run. This looks very similar to being use by the mux. Here the circuit diagram how it will be done:
<div align=center>
    <img src=if_const.png>
</div> 
