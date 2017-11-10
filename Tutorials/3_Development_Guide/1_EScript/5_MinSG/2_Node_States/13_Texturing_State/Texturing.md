<!------------------------------------------------------------------------------------------------
This work is licensed under the Creative Commons Attribution-ShareAlike 4.0 International License.
 To view a copy of this license, visit http://creativecommons.org/licenses/by-sa/4.0/.
 Author: Henrik Heine (hheine@mail.uni-paderborn.de)
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

# Texture Mapping
The easiest way to add textures to a certain scene node is by adding a `TextureState` to it. As every state, it is added directly to a node. If the node is rendered, each state is enabled. In the case of `TextureState`s, this means that it will automatically upload the texture to the correct texture unit of the GPU. Therefore the shader can access the texture correctly. If you have not provided an own shader, the default shader will be used, which renders the mesh using color blending and texture mapping. Typically you will therefore set the color to white.

A `TextureState` is created and used the following way:
```
node += new MinSG.TextureState(texture);
```
where node is a `MinSG.Node`, like a `GeometryNode` or `ListNode`. The `texture` variable must hold a valid OpenGL texture, which could be obtain from file via: `Rendering.createTextureFromFile(file)`
> Remember that any node will inherit all states from their ancestors. Therefore if you add a `TextureState` to a `ListNode`, all child nodes will be textured.

The default shader can only texture the mesh correctly, if the vertices have a texture attribute.

## Example
In this simple example we construct a simple quad mesh and add a chess pattern to it:
<!---INCLUDE src=Texturing.escript, start=14, end=63--->
<!---BEGINN_CODESECTION--->
<!---Automaticly generated section. Do not edit!!!--->
    static Vec2 = Geometry.Vec2;
    static Vec3 = Geometry.Vec3;
    
    var buildMesh = fn() {
        // First we build a simple Mesh, consisting of a single quad
        var mb = new Rendering.MeshBuilder();
        mb.color(new Util.Color4f(1,1,1,1));
        // Vertex 0:
        mb.position(new Vec3(0,0,0));
        mb.texCoord0(new Vec2(0,1));
        mb.addVertex();
    
        // Vertex 1:
        mb.position(new Vec3(10,0,0));
        mb.texCoord0(new Vec2(1,1));
        mb.addVertex();
    
        // Vertex 2:
        mb.position(new Vec3(10,10,0));
        mb.texCoord0(new Vec2(1,0));
        mb.addVertex();
    
        // Vertex 3:
        mb.position(new Vec3(0,10,0));
        mb.texCoord0(new Vec2(0,0));
        mb.addVertex();
    
        // create quad
        mb.addQuad(0,1,2,3);
        // return mesh
        return mb.buildMesh();
    };
    
    // build GeometryNode with corresponding mesh
    var geo = new MinSG.GeometryNode(buildMesh());
    // create chess texture of size 1024*1024, with tiles of side length 64
    var chess = Rendering.createChessTexture(1024, 1024, 64);
    // create new TextureState
    var texState = new MinSG.TextureState(chess);
    // you could also set the TextureUnit:
    texState.setTextureUnit(0); // only needed if you add more than one texture though...
    // add state to node
    geo += texState;
    
    
    // create new scene and add node to it
    var sceneNode = new MinSG.ListNode();
    sceneNode += geo;
    PADrend.registerScene(sceneNode);
    PADrend.selectScene(sceneNode);
<!---END_CODESECTION--->
First of all we just declare two static variable in order to access the `Vec2` and `Vec3` classes without using the full name, like `Geometry.Vec3`. Afterwards we define a simple function, which is used to generate a simple mesh. This mesh consists of only four vertices:
```
(0,10,0) --- (10,10,0)
   |             |
   |             |
   |             |
(0,0,0)  --- (10,0,0)
```
Each vertex gets a 2D vector describing the texture coordinate of this vertex. Texture coordinates are given in texture space, therefore the origin (0,0) lies in the bottom left corner and NOT in the top left corner. Furthemore this space is normalized, therefore the top right corner of any texture, regardlessly of the size, has the coordinate (1,1).
If you specify a value that goes beyond the range (0,0) - (1,1), the behavior depends on the settings. By default it is set to `wrap` mode. This means that only the fractional part of a coordinate is used and therefore it 'wraps around'.

In this simple example we just create a chess texture by using a little helper function:
```
var chess = Rendering.createChessTexture(1024, 1024, 64);
```
The first two arguments specify the size of the texture and the last argument specifies the side length of a single chess tile. Afterwards we create a new texture state.

Now we could change the texture unit of this state, by using `setTextureUnit(unit)`. The default shader will only use texture unit 0, but if you provide an own shader, you could of course use all texture units.





