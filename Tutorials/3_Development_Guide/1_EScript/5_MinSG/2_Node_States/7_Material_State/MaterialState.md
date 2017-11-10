<!------------------------------------------------------------------------------------------------
This work is licensed under the Creative Commons Attribution-ShareAlike 4.0 International License.
 To view a copy of this license, visit http://creativecommons.org/licenses/by-sa/4.0/.
 Author: Florian Pieper (fpieper@mail.uni-paderborn.de)
 PADrend Version 1.0.0
------------------------------------------------------------------------------------------------->
<!---BEGINN_INDEXSECTION--->
<!---Automaticly generated section. Do not edit!!!--->
# Overview
* 3.1.5.2 Node States
    * 3.1.5.2.1 [Introduction to States](../../../../../3_Development_Guide/1_EScript/5_MinSG/2_Node_States/1_Introduction_to_States.md)
    * 3.1.5.2.2 [AlphaTestState](../../../../../3_Development_Guide/1_EScript/5_MinSG/2_Node_States/2_Alpha_Test_State/AlphaTestState.md)
    * 3.1.5.2.3 [BlendingState](../../../../../3_Development_Guide/1_EScript/5_MinSG/2_Node_States/3_Blending_State/BlendingState.md)
    * 3.1.5.2.4 [CullFaceState](../../../../../3_Development_Guide/1_EScript/5_MinSG/2_Node_States/4_Cull_Face_State/CullFaceState.md)
    * 3.1.5.2.5 [GroupState](../../../../../3_Development_Guide/1_EScript/5_MinSG/2_Node_States/5_Group_State/GroupState.md)
    * 3.1.5.2.6 [LightingState](../../../../../3_Development_Guide/1_EScript/5_MinSG/2_Node_States/6_Lighting_State/LightingState.md)
    * 3.1.5.2.7 [MaterialState](../../../../../3_Development_Guide/1_EScript/5_MinSG/2_Node_States/7_Material_State/MaterialState.md)
    * 3.1.5.2.8 [NodeRendererState](../../../../../3_Development_Guide/1_EScript/5_MinSG/2_Node_States/8_Node_Renderer_State/NodeRendererState.md)
    * 3.1.5.2.9 [PolygonModeState](../../../../../3_Development_Guide/1_EScript/5_MinSG/2_Node_States/9_Polygon_Mode_State/PolygonModeState.md)
    * 3.1.5.2.10 [ScriptedStates](../../../../../3_Development_Guide/1_EScript/5_MinSG/2_Node_States/10_Scripted_State/ScriptedStates.md)
    * 3.1.5.2.11 [ShaderState](../../../../../3_Development_Guide/1_EScript/5_MinSG/2_Node_States/11_Shader_State/ShaderState.md)
    * 3.1.5.2.12 [ShaderUniformState](../../../../../3_Development_Guide/1_EScript/5_MinSG/2_Node_States/12_Shader_Uniform_State/ShaderUniformState.md)
    * 3.1.5.2.13 [Texturing](../../../../../3_Development_Guide/1_EScript/5_MinSG/2_Node_States/13_Texturing_State/Texturing.md)
    * 3.1.5.2.14 [TransparencyRenderer](../../../../../3_Development_Guide/1_EScript/5_MinSG/2_Node_States/14_TransparencyRenderer/TransparencyRenderer.md)
<!---END_INDEXSECTION--->

# Gloal of this tutorial
This tutorial shows the usage of materials and their interaction with the Phong illumination model.

# Requierments
You should have understand the tutorial about lighting states.

# Material
As mentioned in the lighting state tutorial, the lights color is divided into three parts, which are ambient, diffuse and specular.
Materials can also be seen to be some kind of color, that is used to color objects.
A lighting model is used to calculate the final color of an object.
In PADrend, by default the phong model is used.
It combines the lights color, the lights position, the viewers position as well as the material to calculate a color value for each surface point of an object.
The Phong equation calculates a color value for each of the three parts and combines them to a single color value.
Therefore also the material contains of three color value (ambient, diffuse, specular).
In addition a shininess value describes whether the material is rough or mirror like.
It takes part in the calculation of the specular part.   
For this tutorial we will create four spheres.
The first three show the single parts of the Phong model individually.
Therefore the material parts that should not be considered by the equation are set to black color.
As mentioned, the Phong equation includes the light sources position.
To ensure the same configuration, each sphere has its own light source, which is positioned relative to it.
A light source and a sphere are connected by an lighting state.

## Creating a sphere
Since the spheres only differ in their position and material, but overall the code for creating them is the same, we introduce a function for the creation.

<!---INCLUDE src=MaterialState.escript, start=14, end=50--->
<!---BEGINN_CODESECTION--->
<!---Automaticly generated section. Do not edit!!!--->
    //creates a geometry node with a material state and attaches a light source to it
    var createNodeWithLightSource = fn(xMovement, Util.Color4f ambient, Util.Color4f diffuse, Util.Color4f specular, shininess){
        //creating a geometry node containing the mesh
        var mesh = Rendering.MeshBuilder.createSphere(50, 50);
        var geoNode = new MinSG.GeometryNode(mesh);
        geoNode.moveLocal(new Geometry.Vec3(xMovement, 0, 0));
        
        //create a material state and setting its properties
        var materialState = new MinSG.MaterialState();
        materialState.setAmbient(ambient);
        materialState.setDiffuse(diffuse);
        materialState.setSpecular(specular);
        materialState.setShininess(shininess);
        
        //creating a new light node having a white light color
       var pointLightNode = new MinSG.LightNode(MinSG.LightNode.POINT);
       pointLightNode.setAmbientLightColor(new Util.Color4f(1.0, 1.0, 1.0, 1.0));
       pointLightNode.setSpecularLightColor(new Util.Color4f(1.0, 1.0, 1.0, 1.0));
       pointLightNode.setDiffuseLightColor(new Util.Color4f(1.0, 1.0, 1.0, 1.0));
       pointLightNode.moveLocal(new Geometry.Vec3(xMovement, 5, 5));
       
       //creating a light state so that only the geometry node ist illuminated by the light source
       var lightingState = new MinSG.LightingState(pointLightNode);
       lightingState.setEnableLight(true);
       
       //adding the states to the node
       geoNode.addState(materialState);
       geoNode.addState(lightingState);
       
       //creating a subtree for the created nodes
       var lstNode = new MinSG.ListNode();
       lstNode += geoNode;
       lstNode += pointLightNode;
       
       //returning the subtree
       return lstNode;
    };
<!---END_CODESECTION--->

First of all a geometry node containing the sphere is created and positioned.
Afterwards a material state is instantiated.
By default the state represents a light gray material.
For each of the three parts there is a setter and getter.
We use the getters to set the materials colors.
The values are passed as parameters to our function.
At last we set the shininess, which is a value between _0_ and _128_.  
Next up we create a light source.
It gains white colored light.
A light state is used to connect it to the geometry node holding the sphere.
Both states get attached to the node by calling _addState_.  
In a last step we create a _ListNode_ which combines both nodes in a common subtree.
This node is returned, so that it can be in the scene graph.

## Creating the nodes
For this tutorial we use a bras like material.
You can find tables on the Internet that provide you with color values for a bunch of materials.
Keep in mind that you need to scale the shininess value to be in between _0_ and _128_.
The values may be provided in a range from _0_ to _1_.
In this case you simply need to multiply the value by _128_.
For the bras material we use:

<!---INCLUDE src=MaterialState.escript, start=52, end=56--->
<!---BEGINN_CODESECTION--->
<!---Automaticly generated section. Do not edit!!!--->
    //color of a brass like material
    var ambientPart = new Util.Color4f(0.2125, 0.1275, 0.054, 1.0);
    var diffusePart = new Util.Color4f(0.714, 0.4284, 0.18144, 1.0);
    var specularPart = new Util.Color4f(0.393548, 0.271906, 0.166721, 1.0);
    var shininess = 25.6;
<!---END_CODESECTION--->

In addition we need a black color to switch off single parts of the Phong equation.

<!---INCLUDE src=MaterialState.escript, start=58, end=59--->
<!---BEGINN_CODESECTION--->
<!---Automaticly generated section. Do not edit!!!--->
    //black color for switching single parts of the phong equation off 
    var blackColor = new Util.Color4f(0.0, 0.0, 0.0, 1.0);
<!---END_CODESECTION--->

Next up we create the four nodes.
The first three nodes represent the three parts of the equation from left to right in the order ambient, diffuse and specular.
The node on the right side shows the illumination created by the Phong model.
In a last step all nodes are connected to build a scene graph.

<!---INCLUDE src=MaterialState.escript, start=61, end=74--->
<!---BEGINN_CODESECTION--->
<!---Automaticly generated section. Do not edit!!!--->
    //creating four nodes
    //the first three nodes show the single parts of the phong equation individually 
    var nodeAmbient = createNodeWithLightSource(-4.5, ambientPart, blackColor, blackColor, 0.0); 
    var nodeDiffuse = createNodeWithLightSource(-1.5, blackColor, diffusePart, blackColor, 0.0); 
    var nodeSpecular = createNodeWithLightSource(1.5, blackColor, blackColor, specularPart, 25.6); 
    //the last node shows the resulting phong illumination
    var nodePhong = createNodeWithLightSource(4.5, ambientPart, diffusePart, specularPart, 25.6);  
    
    //creating the scene graph
    var scene = new MinSG.ListNode();
    scene += nodeAmbient;
    scene += nodeDiffuse;
    scene += nodeSpecular;
    scene += nodePhong;
<!---END_CODESECTION--->

The image below shows the outcome.
You can easily see that the ambient part is constant over the whole sphere.
The diffuse term only depends on the light sources position, so that it is constant as long as the light source does not change.
The specular part depends on the viewers position, so that from different position you can observe changes in the specular reflection.

![Phong illumination](phong.png)




