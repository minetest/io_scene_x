This is an updated version of the .x exporter for use with Blender 2.8 and later,
for those who prefer to export their animated meshes as .x instead of .b3d

This plugin is known to work in Blender versions up to 3.0.0 Alpha.

An extra feature has been added specifically for use with Minetest; NLA strip ranges
may be exported as a serialized Lua table in an extra .nla file, which may be used
to avoid hardcoding animation ranges in your scripts.

### Known issues
Minetest uses a lefthanded Y-up coordinate space, but the plugin supports this space in
a lazy way that breaks bone attachments in MT, by only modifying the root transform.

A possible workaround is to export as righthanded Z-up (Blender space),
and process the file with other tools to change the space to the right one.
Since .x is a text format, it should be possible with a simple script.

If you wish to do this, you will need a swizzle matrix `M` that looks like:
```
1 0 0 0
0 0 1 0
0 1 0 0
0 0 0 1
```
You will then have to:
- Multiply each vertex, normal, position key and scale key by this matrix
- Multiply the XYZ part of each quaternion key by the matrix
- Multiply each bindpose matrix like this: `B = S * ( B * S )`
- Multiply each frame transform matrix like this: `T = ( S * T ) * S`
- Flip all the faces by reversing their index array, including MeshNormals faces

After these operations, the result should be a correctly formed lefthanded Y-up model.
