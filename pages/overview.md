# Overview

The OpenGL Shading Language is actually two closely related languages. These languages are used to create shaders for the programmable processors contained in the OpenGL processing pipeline. The precise definition of these programmable units is left to separate specifications. In this document, we define them only well enough to provide a context for defining these languages.

Unless otherwise noted in this paper, a language feature applies to all languages, and common usage will refer to these languages as a single language. The specific languages will be referred to by the name of the processor they target: vertex or fragment.

## Vertex Processor

The *vertex processor* is a programmable unit that operates on incoming vertex values and their associated data. The vertex processor is intended to perform traditional graphics operations such as:

- Vertex transformation (modelview and projection matrices)
- Normal transformation and normalization
- Texture coordinate generation
- Texture coordinate transformation
- Lighting
- Color material application

Programs written in the OpenGL Shading Language that are intended to run on this processor are called *vertex shaders*. Vertex shaders can be used to specify a completely general sequence of operations to be applied to each vertex and its associated data. Vertex shaders that perform some of the computations in the list above are responsible for writing the code for all desired functionality from the list above. For instance, it is not possible to use the existing fixed functionality to perform the vertex and normal transformation but have a vertex shader perform a specialized lighting function. The vertex shader must be written to perform all three functions.

The vertex processor does not replace graphics operations that require knowledge of several vertices at a time or that require topological knowledge, such as:

- Perspective division
- viewport mapping
- Primitive assembly
- Frustum and user clipping
- Backface culling
- Two-sided lighting selection
- Polymode processing
- Polygon offset
- Depth Range

Any OpenGL state used by the shader is automatically tracked and made available to the shader. This automatic state tracking mechanism allows the application to use existing OpenGL state commands for state management and have the current values of such state automatically available for use in the vertex shader.

The vertex processor operates on one vertex at a time. The design of the vertex processor is focused on the functionality needed to transform and light a single vertex. Vertex shaders must compute the homogeneous position of the coordinate, and they may also compute color, texture coordinates, and other arbitrary values to be passed to the fragment processor. The output of the vertex processor is sent through subsequent stages of processing that are defined exactly the same as they are for OpenGL 1.4: primitive assembly, user clipping, frustum clipping, perspective projection, viewport mapping, polygon offset, polygon mode, shade mode, and culling. This programmable unit does not have the capability of reading from the frame buffer. However, it does have texture lookup capability. Level of detail is not computed by the implementation for a vertex shader, but can be specified in the shader. The OpenGL parameters for texture maps define the behavior of the filtering operation, borders, and wrapping.

## Fragment Processor

The *fragment processor* is a programmable unit that operates on fragment values and their associated data. The fragment processor is intended to perform traditional graphics operations such as:

- Operations on interpolated values
- Texture access
- Texture application
- Fog
- Color sum

Programs written in the OpenGL Shading Language that are intended to run on this processor are called *fragment shaders*. Fragment shaders can be used to specify a completely general sequence of operations to be applied to each fragment. Fragment shaders that perform some of the computations from the list above must perform all desired functionality from the list above. For instance, it is not possible to use the existing fixed functionality to compute fog but have a fragment shader perform specialized texture access and texture application. The fragment shader must be written to perform all three functions.

The fragment processor does not replace the fixed functionality graphics operations that occur at the back end of the OpenGL pixel processing pipeline such as:

- Shading model
- Coverage
- Pixel ownership test
- Scissor
- Stipple
- Alpha test
- Depth test
- Stencil test
- Alpha blending
- Logical ops
- Dithering
- Plane masking

Related OpenGL state is also automatically tracked if used by the shader. A fragment shader cannot change a fragment's x/y position. To support parallelism at the fragment processing level, fragment shaders are written in a way that expresses the computation required for a single fragment, and access to neighboring fragments is not allowed. A fragment shader is free to read multiple values from a single texture, or multiple values from multiple textures. The values computed by the fragment shader are ultimately used to update frame-buffer memory or texture memory, depending on the current OpenGL state and the OpenGL command that caused the fragments to be generated.

The OpenGL parameters for texture maps continue to define the behavior of the filtering operation, borders, and wrapping. These operations are applied when a texture is accessed. The fragment shader is free to use the resulting texel however it chooses. It is possible for a fragment shader to read multiple values from a texture and perform a custom filtering operation. It is also possible to use a texture to perform a lookup table operation. In both cases the texture should have its texture parameters set so that nearest neighbor filtering is applied on the texture access operations.

For each fragment, the fragment shader may compute color and/or depth, or completely discard the fragment.

The results of the fragment shader are then sent on for further processing. The remainder of the OpenGL pipeline remains as defined in OpenGL 1.4. Fragments are submitted to coverage application, pixel ownership testing, scissor testing, alpha testing, stencil testing, depth testing, blending, dithering, logical operations, and masking before ultimately being written into the frame buffer. The primary reason for keeping the fixed functionality at the back end of the processing pipeline is that the fixed functionality is cheap and easy to implement in hardware. Making these functions programmable is more complex, since read/modify/write operations can introduce significant instruction scheduling issues and pipeline stalls.

Most of these fixed functionality operations can be disabled, and alternate operations can be performed within a fragment shader if desired.
