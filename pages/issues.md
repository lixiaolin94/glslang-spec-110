# Issues

## #1 Should the programs that run on these programmable processors be called shaders or programs?

DISCUSSION: Shader fits in with common usage in RenderMan and DX8. There is some argument that shading has connotations of being a color operation so doesn't fit with a vertex operation.

RenderMan doesn't make this distinction, nor does DX8. It seems wise to go along with the common usage of shader as a general term for a program that operates on some part of a graphics pipeline.

RESOLVED on October 12, 2001: The term shader will be used.

Note: Shader is used to denote a single independent compilation unit. Program is used to denote a set of shaders linked together.

CLOSED on September 10, 2002.

## #2 Should there be a separate programmable unit for doing the pixel transfer operations?

DISCUSSION: We originally had the concept of a separate pixel shader where the pixel and imaging operations would be done. On further consideration it seemed very unlikely that anyone would implement this as an independent functional unit but rather do them in the fragment shader behind the scenes. OpenGL treats pixel and fragment operations as mutually exclusive so sharing one processing unit is a natural implementation. Forcing an abstraction that differed from reality seemed to be a hindrance apart from increasing the amount of work.

RESOLVED on October 12, 2001: No, the fragment processor will be used to process both geometry and pixel data.

CLOSED on September 10, 2002.

## #3 Should shaders be allowed to subset the fixed functionality that they replace?

DISCUSSION: There would be a lot of complexity in defining the interfaces to allow subsetting. It isn’t very difficult to write shaders that implement the whole of the graphics processing pipeline.

RESOLVED on October 12, 2001: No, shaders cannot subset the fixed functionality they are replacing. If shaders want to change the lighting in some way then they have to do the other items as well. It will be helpful to have example shaders that fully implement the OpenGL fixed functionality pipeline.

CLOSED on September 10, 2002.

## #4 Should a higher level shading language be layered on top of OpenGL instead of being designed to fit within OpenGL?

DISCUSSION: In the current design, the shading language is integrated into OpenGL and just provides alternative methods to the state controlled pipeline outlined earlier. The Stanford approach is to layer their shading language on top of OpenGL. This has some advantages and disadvantages that will become apparent when the differences are examined.

The Stanford approach uses a higher abstraction level. This helps with writing some kinds of programs where the abstractions match the problem domain. For example treating lights and surfaces as abstract entities makes some 3D graphics operations easier, however OpenGL is now being used for video and image processing where this abstraction is largely irrelevant. Similarly many games have shunned lighting via traditional means and use textures (light maps) instead.

There is nothing in the language or bindings that prevent higher levels of abstractions from being layered on top of a programmable OpenGL. We also wish to keep the overall abstraction level of OpenGL at its current level.

The Stanford approach also provides for different computational frequencies. By having the higher levels of abstraction where one program defines the current graphics operation in total allows the compiler to separate out the parts that need to run at the primitive group level, primitive level, vertex level and fragment level. The compiler can therefore generate the code to run on the CPU, vertex processor and fragment processor as appropriate. This is obviously more complicated to implement than having the programmer specify the programs to run on each part of the pipeline (although some hints are still required by the Stanford language), although this does make the virtualization of the hardware easier as the compiler has the overall view.

The major disadvantage of this is that it forces more intrusive changes to OpenGL to support the clear delineation of the primitives, vertices and fragment operations. Many of the core OpenGL features have been replaced or are not available and it is not possible to use the standard OpenGL

transformation and lighting operations with a custom fragment shader (or vice versa), or to allow one vertex shader to drive multiple fragment shaders. An advantage of the current approach is that the look and feel of OpenGL 1.4 is maintained and it allows a graceful mix and match approach during the transition period from fixed functionality to full programmability.

This is not a criticism of the Stanford work, as they had no choice but to layer on top of OpenGL.

RESOLVED on October 12, 2001: The OpenGL Shading Language should be built into OpenGL, and not layered on top. It is also noted that if this is not the case, OpenGL should still have a standard shading language, so this document still remains. Hence, this issue is not one against this document but one against the OpenGL API.

CLOSED on September 20, 2002, as moved to the API issues list.

## #5 Should the shading model be part of the fixed functionality fragment processing that is replaced by the fragment processor?

DISCUSSION: The shading model selects between Gouraud and flat shading and this would seem natural to have this as part of the functionality replaced by the fragment shader. Flat shading involves knowledge of the primitive type (for the provoking vertex) and this doesn't really belong in the fragment shader. The fragment shader can always assume the color is interpolated and the shading model is flat then the set up calculations for the color gradients can set the gradients to zero.

RESOLVED on October 12, 2001: No, the shading model is not replaced by the programmable functionality of the fragment processor.

CLOSED on September 10, 2002.

## #6 Is alpha testing programmable?

DISCUSSION: The fragment shader has a function to kill fragments so could do alpha-like testing, however the OpenGL pipeline specifies that alpha testing should happen after coverage has modified the alpha value. We do not want to do coverage in the fragment shader so the alpha test remains The OpenGL Shading Language

outside. If the user is happy to do alpha testing before coverage in their own programs then they can do this.

RESOLVED on October 12, 2001: Yes, applications can do alpha testing in a fragment shader, with the proviso that, when done in the fragment shader, it happens before the coverage computation.

CLOSED on September 10, 2002.

## #7 Is alpha blending programmable?

Fragment shaders can read the contents of the frame buffer at the current location using the built-in variables gl_FBColor, gl_FBDepth, gl_FBStencil, and gl_FBDatan. Using these facilities, applications can implement custom algorithms for blending, stencil testing, and the like. However, these frame buffer read operations may result in a significant reduction in performance, so applications are strongly encouraged to use the fixed functionality of OpenGL for these operations if at all possible. The hardware to implement fragment shaders (and vertex shaders) is made a lot simpler and faster if each fragment can be processed independently both in space and in time. By allowing read-modify-write operations such as is needed with alpha blending to be done as part of the fragment processing we have introduced both spatial and temporal relationships. These complicate the design because of the extremely deep pipelining, caching and memory arbitration necessary for performance. Methods such as render to texture, copy frame buffer to texture, aux data buffers and accumulation buffers can do most, if not all, what programmable alpha blending can do. Also the need for multiple passes has been reduced (or at least abstracted) by the high-level shading language and the automatic resource management.

RESOLVED on October 12, 2001: Yes, applications can do alpha blending, albeit with possible performance penalties over using the fixed functionality blending operations.

REOPENED on July 9, 2002: This issue is related to Issue (23) which remains open, so this issue should also remain open.

Another possibility would be to create an extension that allows more flexibility than the current alpha blending allows, but would still be considered fixed functionality.

RESOLUTION: Issue (23) is resolved as allowing frame buffer reads, so this is once again resolved allowing alpha blending, with the caveats listed above.

REOPENED on December 10, 2002. Issue (23) is re-resolved to disallow frame buffer reads.

RESOLUTION: No, applications cannot do alpha blending, because they cannot read alpha.

CLOSED on December 10, 2002.

## #8 Should the language be defined in such a way that it can be implemented on existing hardware?

DISCUSSION: Today’s generation of hardware does have some programmability. It seems desirable to define a language that would work on today’s hardware as well as tomorrow’s.

RESOLVED on October 12, 2001: We have tried to make the shading language forward looking and pitched at a level we believe hardware can attain within a generation or two. We have avoided adding features (such as small fixed point data types or implicit clamping) or dumbing down (removal of loops and functions) the language to better support existing hardware as this is a retrograde step. It would be possible to run a limited subset of shaders on existing hardware but it is not going to be easy for an application to determine in a portable way if the shader will run or if it will produce acceptable results. Overall, the decision here is to set a goal for hardware to strive towards for the next few years.

CLOSED on September 20, 2002.

## #9 Should the concept of the preprocessor for the language be dropped?

DISCUSSION: We could do without the #ifdef by using if (false) and we rely on compiler stripping out code which cannot be reached. The C++ spec seems to be deemphasizing the use of #ifdef but we are still retaining it because it is a common idiom, it is easier to see in the code and it can be used in places where the grammar doesn't allow if (false).

Do we want a vendor specific predefined #define to allow compiler problems to be worked around?

From an idealistic view point no as it provides a back door for extensions, but pragmatically differences will occur. We have already seen instances of shader writers using #define to make shaders more readable (e.g., #define MVP gl_ModelViewProjectionMatrix).

RESOLVED on October 12, 2001: No, the preprocessor should be retained. The preprocessor directives that are supported are #ifdef, #ifndef, #undef, #else, #endif, #pragma, #define token (without arguments), and #error.

Issue (55) is added to address additional preprocessor directives.

CLOSED on September 10, 2002.

## #10 Should the fields representing texture components be named s, t, p, and q?

DISCUSSION: Other alternatives to renaming texture r were considered but rejected because they seemed to be more confusing or error prone.

A. Use red, green, blue, alpha instead for color component selection. This makes the component group mechanism described later too cumbersome.

B. Capitalize either the color or texture component names.

C. Drop the names for color or texture. We didn't want to abandon some notational convenience of one of the important usage of vectors.

D. Change the color component order to be bgra so that the two r components now lined up. This color order is quite alien to OpenGL so this would lead to many confusing situations in how the existing API values mapped to the values the shader used.

RESOLVED on October 12, 2001: Yes, using s, t, p, and q as the names for fields representing texture components is the best choice.

CLOSED on September 10, 2002.

## #11 Should there be two separate active fragment shaders to handle back facing and front facing cases?

DISCUSSION: If the user specifies two fragment shaders, one for front facing fragments and one for back facing fragments, the appropriate one could be run automatically. This probably gives a faster shading rate but it forces the user to maintain two programs (where probably most of the code is common). This could be done transparently by the compiler if an implementation wishes to optimize for this case.

RESOLVED on October 12, 2001: No, a single shader should be used to handle both back facing and front facing geometry.

CLOSED on September 10, 2002.

## #12 Should built-in functions be required to differ by more than just return type?

DISCUSSION: Overloading functions that differed in return type only was considered. However, initial work on the compiler has shown that this facility seriously complicates the semantic analysis of an expression to deduce the return type when it is unambiguous, but buried within an expression.

This may be more complicated than it is worth (and may be the reason why C++ doesn't allow this).

Add on the need for a new syntax for the programmer to use to disambiguate ambiguous cases, and it's simply easier to give functions different names for different return types.

RESOLVED on February 25, 2002: Yes, built-in functions must differ by more than just return type.

CLOSED on September 10, 2002.

## #13 How is the noise function defined to allow consistent behavior from one implementation to the next?

DISCUSSION: The noise function is very useful and plays a role in many shading techniques in RenderMan. It poses a problem in specification (and conformance testing) in that perfectly valid noise functions will give very different results. OpenGL has avoided specifying operations so tightly that different implementation will give pixel exact results — this allows an implementation some latitude in accuracy/performance/cost trade-offs. It also avoids having to specify everything down to minute detail. Perlin (the originator of the noise function) has recognized the desirability of a standard noise function (much like everyone expects the sin function to behave in the same way) and has documented his ideas. Maybe this should be a strongly recommended implementation.

This issue is nearly the same as Issue (36).

RESOLVED on September 19, 2002: No specific implementation of noise is required, but the specification will attempt to define the noise function in such a way that similar results can be achieved from one implementation to the next.

CLOSED on September 19, 2002:

## #14 Should fields be allowed to have numeric selectors (e.g. foo.2)?

DISCUSSION: This breaks the usual convention of identifiers starting with a letter. It makes the language less pure, makes lexical analysis more difficult, and adds constraints on how numbers are expressed.

RESOLVED on September 10, 2002: No, the language should be changed so there are no numeric selectors as suggested in Issue (16).

CLOSED on September 10, 2002.

## #15 Should we allow fields that swizzle the components of a vector?

DISCUSSION: This seems like an overly complicated part of the language, with no additional functionality that couldn't be easily expressed with other parts of the language. On the other hand, the swizzling is exposed in lower level assembly languages. On the third hand, perhaps this is just a

"feature" of hardware that shouldn’t/doesn’t need to be exposed in a high level language. On the fourth hand, some useful examples of swizzling have been demonstrated, and it isn’t very hard to support in the compiler.

RESOLVED on September 10, 2002: Swizzling of components is deemed to be a useful language feature that will be retained.

CLOSED on September 10, 2002.

## #16 Should there be a way to indirectly reference into a vector or matrix?

DISCUSSION: Issue (14) and this one could be simultaneously fixed by adding [ ] as a numeric way of indexing into a vector. Then, one would say foo[2], never foo.2, solving Issue (14), and foo[x] as an indirect reference. Numbers could then be expressed as in C.

RESOLVED on September 10, 2002: Yes, indirect references into vectors and matrices should be allowed in the manner suggested in the discussion above.

CLOSED on September 10, 2002.

## #17 Should gl_Position and other currently "write-only" variables be readable?

DISCUSSION: This is simply a compiler feature, with no implication to hardware support. It's often cumbersome to write code without this feature. The compiler can use temporaries to store intermediate values if necessary. This will make programs a little bit cleaner as well.

On the other hand, the api model of read-only inputs and write-only outputs is probably cleaner for the shader writer.

RESOLVED on September 19, 2002. Yes, "write-only" variables are allowed to be readable.

CLOSED on September 19, 2002.

## #18 How should performance/space/precision hints be provided?

DISCUSSION: One basically agreed on so far is for varying: "varying" means perspective correct, while "fast varying" means take a short cut if it saves time. Perhaps we can define a #pragma for this.

Perhaps this could also be applied in other areas.

RESOLUTION: Performance/space/precision hints and types will not be provided as a standard part of the language, but reserved words for doing so will be.

CLOSED: November 26, 2002.

## #19 Should the built-in function "lookup" be added?

DISCUSSION: Textures can be used as look-up tables, not just textures. The main difference is that look-up tables would have a type associated with the return.

RESOLUTION: Yes, the lookup functions should be added as built-in functions so that shader can express the type of the returned value. Functions like i8texture3 will be added to mean three 8bit ints are being looked up. This will still be called a texture, as it is expected to share texture resources. A generic table lookup that is separate from texture resources is deferred until version 1.1.

CLOSED October 22, 2002.

NOTE: These were later removed as part of fitting this language on OpenGL 1.4, as that does not support textures these functions would operate on.

## #20 Should ints greater than 16 bits be added?

DISCUSSION: The shading language is designed in a way that it does not overburden the hardware designer by requiring a lot of unnecessary and redundant features. Integers are useful and may be more efficient for use as loop counters and array indices. 16-bit integers were added to the language as a concession to efficiency for these cases. The mantissa of a floating point value can be used to do integer operations, therefore hardware designers are not required to have a full integer math unit in addition to the floating point math unit. If this were to end up being the key factor in deciding this issue, integers could be defined to be 23 bits in size (at least for operations within the processor), since this is the size of the mantissa of an IEEE FP32 value. As another data point, Renderman does not support integers at all.

RESOLUTION: There are hardware reasons today to limit ints to 16 bits, so they will be.

CLOSED: November 19, 2002.

## #21 Should vectors or (local variable) arrays of ints be added?

DISCUSSION: The lookup function proposed in Issue (19) could return 3 integer values, for example. Shaders are not just floating point algorithms, but also do things like table lookups, indirection, and other generic algorithmic computation. The language does not have to map directly to hardware. On the other hand, to support this we would have to add ivec2, ivec3, and ivec4, or allow local variable arrays of ints.

RESOLUTION: Yes. Vectors of ints will be added.

CLOSED: November 5, 2002.

## #22 Should recursion be supported?

DISCUSSION: Probably not necessary, but another example of limiting the language based on how it would directly map to hardware. One thought is that recursion would benefit ray tracing shaders. On the other hand, many recursion operations can also be implemented with the user managing the recursion through arrays. RenderMan doesn't support recursion. This could be added at a later date, if it proved to be necessary.

RESOLVED on September 10, 2002: Implementations are not required to support recursion.

CLOSED on September 10, 2002.

## #23 Should the fragment shader be allowed to read the current location in the frame buffer?

DISCUSSION: It may be difficult to specify this properly while taking into account multisampling. It also may be quite difficult for hardware implementors to implement this capability, at least with reasonable performance. But this was one of the top two requested items after the original release of the shading language white paper. ISVs continue to tell us that they need this capability, and that it must be high performance.

RESOLUTION: Yes. This is allowed, with strong cautions as to performance impacts.

REOPENED on December 10, 2002. There is too much concern about impact to performance and impracticallity of implementation.

CLOSED on December 10, 2002.

## #24 Does anything need to be added to the language to allow programs can be compiled at compile time, and not need to have multiple compiled versions saved for OpenGL state changes?

DISCUSSION: It is strongly desired that implementations can generate proper code at compile time, and not have to have multiple compiled versions or later recompilation in case OpenGL state changes at a later time (e.g., not knowing the attributes of a texture map until execution time). Maybe another area where more hints are needed in the language, or maybe hardware evolution can take care of the issues.

RESOLUTION: This is resolved to be a general design goal of the OpenGL shading language... that other issues be resolved with the intent that the object code generated from a shader be independent of other OpenGL state.

CLOSED: November 5, 2002.

## #25 Should we add min and max that take gen-type and a scalar, to match clamp semantics?

RESOLUTION: Yes, these should be added.

CLOSED: September 22, 2002.

## #26 Should the programmability be broken out by function (e.g., light shaders, surface shaders, transform shaders, texgen shaders, etc.) rather than the hardware-centric method (vertex and fragment shaders) in the current proposal?

RESOLVED on December 7, 2001: No, the notion of vertex and fragment shaders fits in much better with OpenGL as a hardware-centric API and has received positive feedback during review.

CLOSED on September 10, 2002.

## #27 Should texture units be specified as a keyword or a number?

DISCUSSION: For the built-in texture access functions, the texture unit is specified as a number.

Should it be defined as a keyword instead? The current feeling is that in certain cases it is more convenient to specify the texture as a programmatic value rather than a keyword.

This issue is actually part of Issue (51).

RESOLVED on December 7, 2001: The texture unit is specified as a number. In certain cases it is more convenient to specify the texture as a programmatic value rather than a keyword.

REOPENED on July 12, 2002: Need further discussion of the real convenience provided.

RESOLUTION: Resolved as Issue (51).

CLOSED October 22, 2002.

## #28 Are global values auto-initialized?

RESOLVED on December 7, 2001: No, global values are not auto-initialized. It may be useful for an implementation to support auto-initialization as a debug mode option, however.

CLOSED on September 10, 2002.

## #29 Should the language support bit-wise operations?

RESOLVED on December 7, 2001: The language itself has support for bit-wise operations. In certain programmable units (pack and unpack processor) these are vital. However, there is a desire to cap the complexity of each programmable unit. For the vertex and fragment processors, Boolean operations are supported but general bit-wise operations are not. This is to avoid requiring full functionality integer processing on top of the already-required floating point capabilities of these processors.

REOPENED on July 12, 2002: Certain bit operations are very useful and cannot be easily emulated with floating point operations. For instance, applications could multiple fields of data into texture components and use the bit-wise operators to extract those values (for instance, using 12-bits of a 16-bit luminance texture to store intensity, and the remaining four bits to store opacity). Giving shaders the abillity to do this type of extraction would be preferable to defining new texture formats.

Another way of handling this functionality is with a built-in function to extract a bitfield out of an integer, as this is one expected use of bit-wise operators.

This interacts with Issue (90). Without integer textures, there is less need to solve this issue.

RESOLUTION: Bit-wise support is deferred to a future release.

CLOSED: December 10, 2002.

## #30 Should internal computations be required to be carried out with 32-bit floating point precision? Or should implementations be allowed to carry out computations with higher or lower precision if they so desire?

DISCUSSION: This issue is related to Issue (33) and Issue (68).

RESOLUTION: It is already implicit that floating point requirements must adhere to section 2.1.1 of version 1.4 of the OpenGL Specification. This is sufficient.

CLOSED: November 26, 2002.

## #31 Can you override the computed LOD or bias within a fragment shader?

RESOLVED on October 12, 2001: The computed LOD may be biased by a value provided by a fragment shader. Built-in texture access functions with an LOD argument are provided for this purpose.

CLOSED on September 19, 2002.

## #32 Are interpolated values perspective correct?

RESOLVED on June 3, 2002: Yes, variables defined as varying are perspective correct.

CLOSED on September 10, 2002.

## #33 Should precision hints be supported (e.g., using 16-bit floats or 32-bit floats)?

DISCUSSION: Standardizing on a single data type for computations greatly simplifies the specification of the language. Even if an implementation is allowed to silently promote a reduced precision value, a shader may exhibit different behavior if the writer had inadvertently relied on the clamping or wrapping semantics of the reduced operator. By defining a set of reduced precision types all we would end up doing is forcing the hardware to implement them to stay compatible.

When writing general programs, programmers have long given up worrying if it is more efficient to do a calculation in bytes, shorts or longs and we do not want shader writers to believe they have to concern themselves similarly. The only short term benefit of supporting reduced precision data types is that it may allow existing hardware to run a subset of shaders more effectively.

This issue is related to Issue (30) and Issue (68).

RESOLUTION: Performance/space/precision hints and types will not be provided as a standard part of the language, but reserved words for doing so will be.

CLOSED: November 26, 2002.

## #34 Should the design of the OpenGL Shading Language include support for shaders that are not real-time in nature?

RESOLVED on September 19, 2002: Yes, the design of the language should take into account applications that are not real-time in nature.

CLOSED on September 19, 2002.

## #35 Should additional types such as point, normal, color, etc. be added to the shading language?

RESOLVED on October 12, 2001: No, these type should not be added. The existing generic vector types can support them all without the need for adding additional types to the language.

CLOSED on September 10, 2002.

## #36 Will everyone have different implementations for smoothstep and noise, or should we try to specify and enforce a common implementation of these?

DISCUSSION: This issue is nearly the same as Issue (13). The definition of smoothstep should be sufficient. OpenGL is not pixel-exact, and the definition of smoothstep is as accurate as it needs to be for the specification.

RESOLVED on September 19, 2002: There is a lot of room for a variety of implementations of the OpenGL Shading language. Even with OpenGL today, it is not expected that pictures produced on two different pieces of hardware will produce identical results. The specification should be written in such a way that implementations will produce very similar (though not identical) results.

Conformance testing for the OpenGL Shading Language is an issue for the OpenGL ARB to wrestle with in the future.

It has further been decided to add a source specification of noise() to the spec. But, this is not done.

CLOSED on September 19, 2002.

## #37 Should the fragment shader functionality to "kill" a fragment be a keyword or a built-in function?

DISCUSSION: Kill feels like a flow control directive similar to break and continue. It's not a function which should be overridden by a user function. The Boolean expression can be evaluated in an if statement that precedes the keyword kill. There's no reason to continue processing once kill is executed, so no reason to disguise it as a function call. kill(boolExpr); as a shortcut for if (boolExpr) kill; is not much savings.

RESOLVED on April 15, 2002: The fragment shader functionality to "kill" a fragment should be a keyword.

CLOSED on September 19, 2002:

## #38 Should the built-in texture and noise functions be available from within the vertex shader?

DISCUSSION: There have been numerous requests to support displacement mapping. This request could be satisfied by allowing the built-in noise and texture functions to be available to vertex shaders as well as fragment shaders. This could be implemented in hardware by having the compiler split the vertex shader into a prolog, a texture/noise access, and an epilog. The prolog would be executed by the vertex processing hardware, and the texture/noise access would be done by the fragment processing hardware. The intermediate results would be fed back through the vertex processing hardware to execute the epilog, and then passed on to the fragment shader for fragment processing. On the other hand, it is possible for applications to do this all on the host CPU.

RESOLUTION: Yes, the texture and noise functions should be made available from within the vertex shader as well.

CLOSED October 22, 2002.

## #39 Should it be defined that interpolated values for varying variables are determined by sampling at the fragment center?

DISCUSSION: This interacts with multisampling and requires further investigation.

RESOLUTION: This is to follow the same rules as outlined in section 3.2.1 of version 1.4 of the gl specification.

CLOSED: November 26, 2002.

## #40 Should unsigned ints be supported in the language for vertex and fragment processing?

DISCUSSION: This issue is related to Issue (29). If we allow bit-wise operators, there probably needs to be a way to specify either signed or unsigned integers. Currently unsigned ints are defined only for the pack and unpack shader languages, not for the vertex and fragment languages.

RESOLUTION: Because it has been resolved that integers carry 16 bits of precision, in addition to a sign bit, it is not necessary to introduce an unsigned integer type.

CLOSED: November 26, 2002.

## #41 Are gl_FrontMaterial and gl_BackMaterial attributes or uniforms?

DISCUSSION: The spec currently defines these as attribute arrays, but the spec also says that arrays are not allowed for attributes. If we want to treat them as uniforms, they can remain as arrays.

Otherwise, we should change the names and definitions so as to not use arrays (i.e., give each attribute a unique name).

RESOLVED on September 19, 2002: These will be treated as uniforms. Moving forward, we would rather encourage applications to use user-defined attributes if these need to be changed at every vertex.

CLOSED on September 19, 2002.

## #42 Should there be a way to specify that the transformed position generated by a vertex shader should be invariant with respect to the fixed functionality pipeline?

DISCUSSION: This feature was requested by an ISV. Without it, on many graphics architectures, it may be impossible to precisely match geometry rendered using a vertex shader with geometry rendered using the fixed functionality path.

One possible solution is to change the spec where it says that the built-in variable gl_Position must be written by all vertex shaders. Instead, if the variable gl_Position is not written by the vertex shader, the vertex position will be transformed in a manner that is invariant with respect to the fixed functionality pipeline. If gl_Position is written by the vertex shader, the resulting position may or may not be invariant with respect to the fixed functionality pipeline.

But this solution increases the risk that a shader writer might inadvertently fail to write gl_Position, which will now not generate an error but rather invariant transform gl_Vertex. So here is a possible set of alternative resolutions. Since we have built-in functions, a built-in function might be a clean solution to the request for an invariant transformation. All three built-in functions below could provide for invariant transform of gl_Vertex. Alternative (A) will be most familiar to RenderMan shader writers (minus the named spaces). Alternatives (B) and (C) only provide for invariant transform, with (B) allowing the input to be specified while (C) implicitly inputs gl_Vertex.

The original suggested resolution, and these alternative resolutions, solve the ISV request, but in different manners.

(A) Built-in function:

genType transform( [mat xform,] genType coord ) If matrix is specified, return xform\*coord. Else, transform coord invariant to fixed function method.

Examples:

``` glsl
// transform by MVP, not necessarily invariant with fixed function.
gl_Position = transform( gl_ModelViewProjectionMatrix, gl_Vertex );

// transform invariant with fixed function.
gl_Position = transform( gl_Vertex );
```

(B) Related to (A), but no optional matrix:

genType transform( genType coord ) Transform coord invariant to fixed function method Example:

``` glsl
// transform invariant with fixed function.
gl_Position = transform(gl_Vertex);
```

(C) Related to (B), but no arguments, rename function:

vec4 fixedtransform() Output invariant with fixed function method, implicit input is gl_Vertex.

Example:

``` glsl
gl_Position = fixedTransform();
```

RESOLUTION: Use option C from above.

CLOSED October 22, 2002.

## #43 What is definition of built-in derivative functions of gl_FB\*?

DISCUSSION: An short example fragment shader demonstrates the question best.

``` glsl
void main(void)
{
  gl_FragColor = dFdy(abs(gl_FBColor));
}
```

Earlier whitepapers allowed general framebuffer read within the fragment processor. OpenGL

generally only specifies the fragments to be generated by rasterization, not the order the fragments are generated by rasterization. So general framebuffer reads within the fragment processor could lead to undefined behavior.

Later whitepapers permit only restricted framebuffer reads within the fragment processor. (The pixel at the xw, yw window coordinates of the fragment.). So the question becomes, do the built-in derivative functions conceptually require an implicit general frambuffer read (at least in the immediate neighborhood of the pixel at the xw, yw window coordinates of the fragment)? What does

"at any given point in time" mean in this context?

Possible resolutions:

a) Don't allow gl_FB* read operations in the fragment processor. (This interacts with Issue (23))

b) The built-in derivative functions are undefined if a gl_FB* is a parent of an expression. (The built-in derivative functions are in some cases undefined within the body of a conditional or loop.) Rejected resolutions:

c) Explicitly define the order which fragments are rasterized by OpenGL.

RESOLUTION: gl_FB\* have been removed. Issue (23) has been reopened and closed as disallowing frame buffer reads.

CLOSED on December 11, 2002.

## #44 Should the uniform variables that represent current OpenGL state be available only to specific processors or available to any processor?

DISCUSSION: The current specification is biased toward vertex lighting and fragment shading.

Currently, OpenGL state represented as built-in uniform variables is available only to a specific processor (e.g., lighting state is available only to the vertex processor). This makes it unnecessarily difficult for the fragment shader to do lighting calculations with the OpenGL state. The specification should be agnostic about which shaders will need access to what built-in uniform OpenGL state.

RESOLUTION: OpenGL state that is encapsulated as a uniform variable should be accessible to any processor.

CLOSED October 22, 2002.

## #45 Should naming conventions for OpenGL state be the same as those adopted for the ARB_vertex_program extension?

DISCUSSION: Where the OpenGL Shading Language defines gl_ModelViewMatrix to refer to a specific piece of OpenGL state, the ARB_vertex_program extension uses state.matrix.modelview.

Should the conventions be the same for consistency?

The OpenGL Shading Language conventions for referring to GL state were developed before it was clear that an ARB vertex program extension would even be possible due to IP issues and lack of consensus. ARB_vertex_program (and ARB_fragment_program) state binding might confuse some to think of the syntax as a structure in a C-like language. (Less risk of this confusion in an assembly-ish language.) And ARB_vertex_program and ARB_fragment_program packs state into vec4s.

There is less of a need for such packing in a C-like language.

RESOLVED on August 13, 2002: No, the conventions need not be the same. There isn’t enough interest in making this name change at this point.

CLOSED on September 10, 2002.

## #46 What is the expected behavior for general derivatives at object silhouettes?

DISCUSSION: It would have to be the local instantaneous derivative if it's to be used for filter width or lod computation. That pretty much dictates that no implementation can look to neighboring fragments to compute derivatives, since it is always possible to construct an object that hits only one fragment (and so has no valid neighbors).

RESOLUTION: See derivative section of paper.

CLOSED on December 4, 2002.

## #47 Should the derivative functions have names that are more similar to those used in Renderman?

DISCUSSION: The naming of the derivative functions is somewhat at odds with the precedent established by RenderMan, where Du(f) and Dv(f) compute df/du and df/dv respectively. dPdu and dPdv are potentially more accurate, but functionally equivalent to Du(P) and Dv(p), where P is the built-in variable for 3D location of the sample. We should at least consider Dx() and Dy() for the names of the OGL2 derivative functions.

RESOLUTION: Names are changed to dFdx, dFdy.

CLOSED on December 4, 2002.

## #48 Should a dPdz (or Dz) function be added?

DISCUSSION: If someone wanted to derive df/du and df/dv We'd also need dPdz() or Dz() to avoid a singularity at object silhouettes

RESOLUTION: This is not added.

CLOSED on December 4, 2002.

## #49 Should the shading language include structs?

DISCUSSION: The shading language should support structs. Structs provide a clean way of grouping data to create abstract data types. They are convenient for developers and are supported in C and other generic programming languages.

On the other hand, no compelling case for adding structs to the language has been made. It could help us get the language finalized sooner if we left this till a later rev of the language specification. But, if structures were added, it would be nice to define the lighting state in terms of structs. Vital Images (ISV) indicates they would like to have structs in the language.

RESOLVED on September 19, 2002: The shading language will include structs.

CLOSED on September 19, 2002.

## #50 Should the vertex processor be defined in a way that allows it to perform tessellation of curved surfaces?

DISCUSSION: The issue of geometric LOD and curved suffices is so complex and is so continuously developed that no piece of hardware simpler then a general purpose programmable CPU is up for the job. Any choice of primitive, will be heavily disputed. And most of the popular curved surface primitives like creased subdivision surfaces or trimmed NURBS are not easily implemented in hardware.

If we look at the problem form a performance view, the generation of LODs are generally not the problem since new LODs don't need to be generated to often, only when the geometry in a significant way has moved closer or further away from the camera. The problem comes in when we have animation of interactive manipulation of the surface. In these cases the topology doesn't change so this can be solved on a very efficient way. We simply store a list of references to CVs and weighting factors for each vertex in the LOD.

This simple code can accommodate all types of curved surface:

``` glsl
for(i = 0; i <vertex_count; i++)
{
  x = 0;
  y = 0;
  z = 0;
  for(j = 0; j < *influence_list_length; j++)
  {
    index = *index_array++;
    value = *value_array++;
    x += value * control_vertex_array[index].x;
    y += value * control_vertex_array[index].y;
    z += value * control_vertex_array[index].z;
  }
  surface_vertex_array[i].x = x;
  surface_vertex_array[i].y = y;
  surface_vertex_array[i].z = z;
  influence_list_length++;
}
```

This could be integrated in the vertex shader to allow for maximal flexibility, but this means that vertex shaders must have the ability to do random access arrays of data.

RESOLUTION: Postponed to a future version of this specification.

CLOSED: October 22, 2002.

## #51 Should the language provide some mechanism to distinguish variables that are position independent from those that aren’t?

DISCUSSION: Comments have been made along the lines of "should we really expose SIMD

semantics in the language?" Response:

• What's really being introduced is position independence.

• The existing 'uniform' and 'varying' already introduce this concept, this proposal just completes it.

• Most hardware will benefit from it.

Feasibility: It's possible for a compiler to do sufficient data-flow and control-flow analysis to find all paths that could lead to the assignment of a position independent variable. It may find extra paths, but is not allowed to miss any actual paths. From this, a compiler can prove if a position independent variable only takes on values that are position independent. It may, on rare occasion say a position independent variable is not so, and be wrong, but these will typically occur in degenerate code.

Global Uniforms. It's asking too much of a compiler to do cross-function data-flow analysis, especially across different compilation units. So, the idea of global read/write position independent variables is not supportable and not proposed. Hence, there is no conflict between these uses of

'uniform'.

Output Uniform Parameters. Same problem as uniform globals. Uniform globals and parameters must be read only.

A past alternative was to use the 'int' type as a hint to the compiler that a value was position independent, like for loop indexes, texture ids, array subscripts, etc. This was troublesome, because floating point based control flow could make the hint invalid, leaving the compiler as burdened as it would be without the hint. And/or it made the 'int' type less useful, forcing it to adhere to the proposed 'uniform' semantics. This was too much tying together of otherwise independent ideas.

This issue is related to Issue (27).

RESOLUTION: Use the 'uniform' qualifier to identify locally scoped variables, function return values, and function parameters as being position independent. Local 'uniform' variables cannot be written to with values that were derived from position. Functions declared to return a 'uniform' can only return values not derived from position.

The compiler may return a warning if there is a statically identifiable path through the code that leaves a position dependent derived value in a position independent variable. That is, if a variable is declared uniform or passed to a uniform parameter, the compiler will issue an error if it can't prove the variable is always position independent.

CLOSED October 22, 2002.

## #52 How should resource limits for the shading language be defined?

DISCUSSION: Various proposals have been discussed. One very important consideration is to end up with a specification that provides application portability (e.g., ISVs do not need to support multiple rendering back ends in order to run on all the different flavors of hardware). ISVs definitely would prefer the specification to say that the shading language implementation is responsible for ensuring that all valid shaders must run.

RESOLUTION: Resources that are easy to count (number of vertex processor uniforms, number of fragment processor uniforms, number of attributes, number of varying, number of texture units) will have queriable limits. The application is responsible for working within these externally visible limits.The shading language implementation is responsible for virtualizing resources that are not easy to count (number of machine instructions in the final executable, number of temporary registers used in the final executable, etc.).

CLOSED on October 29, 2002, as being part of the API issues list.

## #53 How are user clip planes handled if the coordinate spaces are separated by a transform that is non-linear?

DISCUSSION: The shading language specification relies on the standard definition of GL clipping.

This works as long as the coordinate spaces are only separated by a linear transformation, however the shading language also lifts these restrictions.

SUGGESTED RESOLUTION: Adopt the "clip coordinate output" approach found in certain NVIDIA proposals for ARB_vertex_program (removed long before the spec was final). This approach provides fully programmable user clipping, not dependent on any semantics of the program or any analysis thereof; and it does not leave most cases undefined.

RESOLUTION: Specify that user clip planes work only under linear transform. It is currently undefined what happens under non-linear transform.

CLOSED October 22, 2002.

## #54 How are global pixel operations (e.g., histogram, min/max) supported?

ADDED on September 10, 2002.

DISCUSSION: The current specification allows access only to the fragment at the current location (though this is open for discussion as per Issue (23)). Operations that require access to other fragment locations in the frame buffer or on the incoming data stream are expressly prohibited. How will the shading language provide functionality that supports global pixel operations such as histogram and min/max?

The desire is to run a program that operates on multiple fragments (similar in spirit to how to run a vertex program that generates new geometry, Issue (50)).

RESOLUTION: Postponed to a future version of this specification.

CLOSED: October 22, 2002.

## #55 Should the preprocessor have any directive in addition to those already defined?

ADDED on September 10, 2002.

DISCUSSION: A number of preprocessor directives could be added to the language specification, for instance, #if, #elif, #include, #define token(...) (with arguments), ## (token pasting), #line, #error, #

(by itself), # (to make a token into a string), defined(token) (and all the other operators, &&, |, +, etc.), and predefined macros, like **DATE**, **FILE**.

In particular, #if would be a useful addition to support processing of versions, dates, and the like. But this necessitates bringing in the ||, &&, >, <, ! operators.

RESOLVED on September 24, 2002: The shading language preprocessor will essentially have all the capability of the C preprocessor except that the #include directive is not supported and string-based directives are also not included.

CLOSED on September 24, 2002.

## #56 Is it an error for an implementation to support recursion if the specification says recursion is not supported?

ADDED on September 10, 2002.

DISCUSSION: This issues is related to Issue (22). If we say that recursion (or some other piece of functionality) is not supported, is it an error for an implementation to support it? Perhaps the specification should remain silent on these kind of things so that they could be gracefully added later as an extension or as part of the standard.

RESOLUTION: Languages, in general, have programs that are not well-formed in ways a compiler cannot detect. Portability is only ensured for well-formed programs. Detecting recursion is an example of this. The language will say a well-formed program may not recurse, but compilers are not forced to detect that recursion may happen.

CLOSED: November 29, 2002.

## #57 Should there be a standard way for applications to invoke debug mode?

ADDED on September 10, 2002.

SUBSUMED by Issue (67).

CLOSED on September 24, 2002.

## #58 Should the language include a list of reserved words?

ADDED on September 10, 2002.

DISCUSSION: Currently the specification does not contain a list of reserved words. Without such a list, valid shaders might become invalid when we make additions to the language in the future.

SUGGESTED RESOLUTION: Yes, the language should include a list of reserved words, including the following: struct, union, enum, typedef, template, goto, switch, default, inline, noinline, long, short, double, sizeof, volatile, public, static, namespace, using, asm, cast, half, fixed, and all tokens that contain two consecutive underscores.

RESOLVED on September 24, 2002: Yes, the shading language should include a list of reserved words.

CLOSED on September 24, 2002.

## #59 How should function parameters be passed?

ADDED on September 10, 2002.

DISCUSSION: Today the specification says that function parameters are call by reference, no aliasing is allowed and output is used for output parameters. This has some non-obvious problems: (A) Uniforms and other globals cannot be passed in as parameters, as that would create an alias. (B) Varyings and other write-only variables are very tricky to pass by reference, as there is nothing that
says a parameter is write-only. (C) If a shader writes into a "pass by reference" parameter, it should either update the caller's argument, or the shader should generate an error because it was not an output parameter. However, expected usage seems to be that it's all right to write to a non-output, the effect is just local.

The specification could be changed to say that function parameters are call by value-return, which means the following: (A) A parameter with no qualifier means the parameter is copied in from the caller at call time. (B) The qualifer output (or out) means the parameter will be copied back to the caller at return time, but not copied in at call time. (C) The qualifier input output (or inout) means the parameter is both copied in and copied back.

These semantics solve all the parameter-related aliasing problems. The compiler doesn’t have to check for aliasing, it can compile as if there is no aliasing, and it's well-defined to the shader writer what happens if they pass parameters in a way that looks like aliasing. These semantics also allow for write-only variables to be passed to a function. Finally, this solution allows writing to a non-output parameter, while making it clear it's only a local copy that gets modified.

RESOLVED on September 24, 2002: Change the spec to say that function parameters are call by value-return as defined above.

CLOSED on September 24, 2002.

## #60 How should the built-in names for lighting state be defined?

ADDED on September 11, 2002.

DISCUSSION: The current names for lighting state ( gl_Light0..n[8] and the associated predefined array index values) make it awkward to write a loop to process lights. This issue is related to Issue (49).

RESOLVED on September 24, 2002: Structs should be added to the shading language, and the lighting state should be redefined as an array of light structs.

CLOSED on September 24, 2002.

## #61 Should user-defined functions be allowed to redefine built-in functions?

ADDED on September 13, 2002.

DISCUSSION: It’s not clear that there is anything to be gained by allowing this. If users inadvertently use the same name as a built-in, they will get unexpected behavior or a drop in performance or both.

RESOLUTION: Yes. This is normal behavior for a language and a library.

CLOSED on September 10th, 2002.

## #62 Should the language include texture gen coefficients for eye/object plane?

ADDED on September 13, 2002.

DISCUSSION: Issue raised by Kent Lin of Intel.

RESOLUTION: Yes, this state should be added.

CLOSED October 22, 2002.

## #63 Should the language include a built-in variable for the projection matrix?

ADDED on September 13, 2002.

DISCUSSION: Built-in variables are already defined for the model-view matrix and the modelview-projection matrix. Should a built-in variable be added for the projection matrix as well?

RESOLUTION: Yes, this state should be added.

CLOSED October 22, 2002.

## #64 Should built-in variable names be added for the state introduced in OpenGL 1.4?

ADDED on September 13, 2002.

DISCUSSION: The specification is currently written against OpenGL 1.3, therefore it does not contain the point parameter states and fog coordinate state. Should these be added?

RESOLVED on September 24, 2002: Yes, we should add built-in variable names for the state introduced in OpenGL 1.4.

CLOSED on September 24, 2002.

## #65 Should mat \* mat perform a matrix multiply or a component-by-component multiply?

ADDED on September 16, 2002.

DISCUSSION: Currently the specification states that the "\_" operator will cause a component-by-component multiplication if two matrices are specified. The multiply operator (\_) does the expected linear algebra operations for scalar \_ scalar, scalar \_ vector, and matrix \_ vector but not for matrix \_

matrix where it is component-wise instead, which is a comparitively rare operation. This was done for consistency with other operators that behave component-wise. (E.g., we probably do not want matrix / matrix to be real matrix division instead of component-wise. Should matrix \* matrix be changed to indicate a matrix multiplication operation?

RESOLUTION: The specification should be modified to indicate that the "\*" operator will cause a matrix multiply if the two operands are matrices.

CLOSED October 22, 2002.

## #66 What should the specification say about the length of time a shader is allowed to run?

ADDED on September 16, 2002.

DISCUSSION: Earlier versions of the white paper talked about a watchdog timer. Is such a thing necessary as part of the language specification?

RESOLUTION: The language specification should not say anything about the length of time a shader is allowed to run. Timeouts, interactivity, and detecting malicious shaders are implementation and/or operating environment details.It probably should be somewhere in the GL2 extension specification(s) that an executing shader is terminated if the application that caused execution of that shader is terminated.

CLOSED October 22, 2002.

## #67 Should there be a standardized way to specify debugging and optimization levels?

ADDED on September 16, 2002.

DISCUSSION: Four alternatives are possible. (A) We don't debug, and we always optimize, so there is no problem. (B) We add debug and optimize parameters to the entry points for compiling and linking. (C) We use #pragma to specify debug and optimization levels, and outline basic portable meanings. (D) We say this is entirely platform dependent, and don't specify anything.

(A) seems short-sighted because turning optimization on/off is a technique and work-around for tracking down some kinds of defects, we will eventually want to debug shaders, and there will be compile-time vs. run-time trade-offs (e.g. if an application is dynamically generating shaders that have really short life-times, it may be faster to turn off slower optimizations).

(B) seems a bit awkward as there will be platform dependent aspects to these activities. (D) seems to be going to far for something that's going to, in principle, exist on all platforms.

RESOLUTION: Use #pragma to specify debug and optimization levels and outline basic portable meanings.

CLOSED October 22, 2002.

## #68 Should the language support explicit data types such as 'half’ (16-bit floats) and 'fixed' (fixed precision clamped data type)?

ADDED on September 17, 2002.

It is common for high level languages to support multiple numeric data types, to allow programmers to choose the appropriate balance between performance and precision. For example, the C language supports the float and double data types, as well as a variety of integer data types. This same general consideration applies for a shading language.

For shading computations, precisions much lower than 32-bit floating point are often adequate. Until recently, most graphics hardware performed all shading computations in 9 or 10 bit fixed-point arithmetic. Lower-precision data types can be implemented with higher performance, especially when the data must travel off chip (e.g. texture data). For this reason, it is desirable to provide access to data types with precision of less than 32 bits in a hardware shading language.

Issue (33) discusses precision hints. Precision hints are less useful than additional data types, because precision hints do not allow function overloading by precision. Developers find it very convenient and useful to be able to have functions with same names and argument lists with different precision data types.

It is also important to be able to specify data type per variable (as opposed to per-shader), because it is common for some computations (e.g. texture-coordinate computations) to require higher precision than others.

On the other hand, there is a desire to ensure that shaders are portable between different implementations. In order to achieve portability, implementations that don’t have native support for half will be penalized because they will have to clamp intermediate calculations to the appropriate precision. If these additional data types are hints that the compiler can choose to do the calculations to lower precision then this leaves the ISV open to unintended clamping or overflow semantics so different architectures can give very different results. The hint also implies that there is a well specified way to convert to between types under the hood so function overload resolution gets more complicated and additional rules are needed to resolve ambiguities, unless all legal combinations of functions must be supplied. Specifying all legal combinations requires adding quite a large number of additional function types (dot product will need { float, half, fixed} \_ { float, half, fixed} \_ number components or 36 versions (vs 4 with only float).

If the additional data types are real types then what can they be applied to? If it is to uniforms and attributes then the different sizes now reflect in the API, but half and fixed have no native support in C. If a half is followed by a float does this mean a float has to start on a 16 bit boundary? What about The OpenGL Shading Language

packing of fixed - the true size is undefined. If half and fixed are just restricted to temporaries then this makes things easier but now the storage efficiency benefit is lost.

The OpenGL spec currently says "The maximum representable magnitude of a floating-point number used to represent positional or normal coordinates must be at least 232." Should we introduce something that runs counter to this? s10e5 precision is inadequate for texture coordinates even for a 1k by 1k texture. It seems that half-floats open a door for precision issues to propogate throughout a shader.

RESOLUTION: Performance/space/precision hints and types will not be provided as a standard part of the language, but reserved words for doing so will be.

CLOSED: November 26, 2002.

## #69 Should the fragment shader be able to access a varying variable that provides the position?

ADDED on September 17, 2002.

DISCUSSION: Window position can be very useful in certain fragment shaders. For example, it can be used to implement stipple patterns using fragment shaders.

RESOLUTION: The window position is part of the built-in variable gl_FragCoord that is available within the fragment processor. The specification should be modified to make it clear that the x and y values of this variable define the window position of the fragment.

CLOSED October 22, 2002.

## #70 Should the language support boolean vectors (e.g. bool2, bool3, bool4), and corresponding vector operators?

ADDED on September 17, 2002.

DISCUSSION: The language supports short float vectors (e.g. vec2, vec3, vec4), so it would be consistent to support boolean vectors as well. If the language includes support for boolean vectors and operations, it is simple to express elementwise vector computations. For example, the min and max vector operations can be elegantly implemented within the language using bool-vector operations.

When a comparison operator such as '<' is applied to vector operands, the result is a boolean vector that contains the result of the elementwise comparison. The '?:' construct operates in elementwise fashion if the first operand is a boolean vector. if, while, and for still require a scalar boolean value RESOLUTION: Vectors of bool will be added. But C-like short-circuit evaluation of && and || will be kept. For this release, if and ?: will select based only on scalar bools. Since == and != should also return a scalar bool (as they do for all types, including struct), they will do so, and not return a vector of bool when vectors are compared. Hence, <, >, <=, and >= also will not create vector of bool.

They will not legally operate on vectors. Rather, built-in functions will be added for relational operations on vectors.

CLOSED: November 5, 2002.

## #71 Should the shading language support compound data structures such as arrays of arrays, structs of arrays, arrays of structs, etc.?

ADDED on September 17, 2002.

DISCUSSION: The ability to create compound user-defined data structures is a fundamental part of almost all high-level programming languages. Omitting support for these capabilities would be
inconsistent with the generally forward-looking nature of the OpenGL shading language design effort.

On the other hand, as with the issue of adding structs (Issue (49)), a case can be made for every language that it will be useful to someone, somewhere, sometime. Is it worth taking the time and effort to ensure that this functionality is part of the first version of the specification? Will it cause any problems to defer it and add it later?

RESOLVED on September 24, 2002: Yes, the shading language should support arrays of arrays, structs of arrays, arrays of structs, etc.

CLOSED on September 24, 2002.

## #72 Should the shading language include a switch statement?

ADDED on September 19, 2002.

DISCUSSION: The C language switch statement is a useful construct in writing clean code. The alternative is to use a less readable collection of if statements.

RESOLUTION: Switch will not be added for initial release. There is desire to manage floating point ranges, which would take a long time to work out.

CLOSED on October 1, 2002.

## #73 What are the semantics of the keyword return in the main() function?

ADDED on September 19, 2002.

DISCUSSION: Currently the specification does not say what happens if the keyword return is included in the main() function in any place other than the very end of the function.

RESOLUTION: return means exit main, just like getting to the end. It does not mean kill.

CLOSED on October 1, 2002.

## #74 How is data computed by the vertex or fragment shader communicated back to the application?

ADDED on September 24, 2002.

DISCUSSION: This issue is related to Issue (50) and Issue (54). If either the vertex processor or the fragment processor are allowed to compute results that don’t continue down the processing pipeline (e.g., histogram, min/max, or computing vertex array data from control points), how will those results be communicated back to the application?

RESOLUTION: Postponed to a future version of the specification.

CLOSED: October 22, 2002.

## #75 Should uniforms and attributes which are initialized by the application allowed to be structs?

ADDED on September 25, 2002.

DISCUSSION: If we don’t allow this, the application has to pass all data via individual global variables, and shader code must pack the data together in structs in order to use structs. This causes ugly unnecessary shader code. On the other hand, initializing struct data should not involve a complexification of the API.

Also, it seems broken to have structs in the language, but not have a way to initialize them from the application. On the other hand, a complete solution for initializing structs seems beyond this release.

It’s also been noted that attributes can be matrices, but there is no API for initializing them.

RESOLUTION:

- Add entry points to initialize an attribute matrix. At bind time, a 4x4 matrix takes 4 consecutive locations, a 3x3 matrix takes 3 consecutive locations, and a 2x2 matrix takes 2 consecutive locations.

Details of layout are hidden. If an implementation only needs one slot for a 2x2, it only has to use one slot, but the room is there for implementations that need two.

- Don't yet support arrays of attributes and structs of attributes.

- Allow uniform struct and array of struct.

- Support API initialization of struct members by specifying a string at GetUniformLocation time that selects the member to be initialized. E.g. "struct.member", "struct[4].member",

"struct[2].member[2]" etc.

- Don't yet support struct-level initialization in the API, wait for future full solution that understands strides, alignments, padding, etc.

CLOSED October 22, 2002.

## #76 Should vec2, vec3, vec4, mat2, mat3 and mat4 be defined as structures?

ADDED on September 26, 2002

DISCUSSION: Treating these types as structs is that the language would get much clearer and would be easier to specifiy. This also will simplify compiler development.

Currently, these types are involved in special language features like"v.xyz" syntax and swizzling operations like "v.yzx". However, such operations are not neccessary in a HLSL.For example, swizzling operations are typical for assembly language tricks, for example to express a cross product with two assembly commands. But such tricks are not necessary in a HLSL, you simply use build-in functions, or define user-defined functions if really needed.Furthermore, in the rare cases where such operations are really needed they can be easily expressed by standard language features like vec3(v.y, v.z, v.x).

Furthermore, a syntax like v\[i\]\[\] is planned to be added as special language feature.But situations where you need dynamic indices for componentsare quite rare and can be easily expressed by buildin access functions.

Another aspect is having different names for identical components,for example xyzw versus rgba.

Loosing this feature is probably this is the only real disadvantage of treating vec3 and vec4 as normal struct.On the other hand, having unique names xyzw also may clarify shader programs.

On the other hand, the vector and matrix type are indeed special: (A) Most operators work on them.

Not so for struct. (B) Every element of a vector or matrix is the same type. Not so for struct. (C) A matrix is conceptually two-dimensional, while structs are conceptually one-dimensional. The current language isn't perfect at keeping a matrix 2D, given we have to sometimes know its column major order, but it can be treated as 2D sometimes. (D) Hardware may have special hardware for vector and matrix types that are more difficult to map to generic structs than to built-in types.

RESOLUTION: vectors and matrices are not structures, but some changes will be made. Summary:

- R-value swizzling becomes an operation on an expression. This is a change from the initial spec saying it was a member selector on a vector variable.

- We remove the empty-brackets syntax.

- We keep the swizzling syntax we already have for vectors.

- We keep the array access syntax for vectors.

- We add, for matrix m, that m\[i\] is the ith column and is vector type. Both l-value and r-value.

- Because m\[i\] is a vector, and vectors have array syntax, then it just falls out that m\[i\]\[j\] is the ith column, jth row of m.

CLOSED on October 8, 2002.

## #77 Should the type of gl_FragStencil and gl_FBStencil be changed to int?

These are currently floats. The integer type has progressed in the language, so this should be reconsidered.

RESOLUTION: Yes. However, stencil writing has been deferred to a future release, and frame buffer reading has been removed.

CLOSED on December 10th, 2002.

## #78 Are stencils automatically clamped to the current min and max values that can be stored in the stencil buffer?

RESOLUTION: Yes. However, stencil writing has been deferred to a future release.

CLOSED on December 10th, 2002.

## #79 Do we need to have near and far clipping planes available to the fragment shader

DISCUSSION: ARB_fragment_program has these.

RESOLUTION: Yes.

CLOSED on December 10th, 2002.

## #80 Rectangular (non-power of 2) textures aren’t indexed by 0.0 to 1.0, but rather by their actual dimensions. Is this a problem?

DISCUSSION: Yes this is a problem. Some hardware needs to know at compile time what kind of texture is being accessed. We want to avoid having to recompile shaders due to state changes. More texture built-in names could be used, so it is known at compile time what kind of texture is being accessed. Something like textureRect3 to mean a rectangular texture returning 3 components. It is also the case that rectangular textures are not a part of OpenGL 1.4, so these functions could be added as an extension, and not be part of this release of the language specification.

RESOLUTION: Make room for adding more functions to support other texture types, but defer doing this for rectagular textures until they have become part of core OpenGL.

CLOSED on December 17th, 2002.

## #81 Should we support a way of accessing fixed functionality fog from a shader, to take advantage of possible fog that may exist in fixed functionality hardware?

DISCUSSION: This sounds similar to supporting a fixed functionality transform, which we do.

However, that was done out of need for invariance, which is not an issue with fog. It is also provided in ARB_fragment_program, but the spirit of this spec is to replace fixed functionality and extra ways of accessing it with programmability.

RESOLUTION: No special support of a fixed pipeline fog access will be provided.

CLOSED on December 17th, 2002.

## #82 Is it really necessary to require writing of gl_Position, gl_FragColor or gl_FragDepth?

DISCUSSION: This can be difficult to handle error cases for when the writes are conditional. There was also discussion that either kill should be called, or a gl\_ output be written in a fragment shader.

However, that is irrelevant, as it’s okay to neither kill nor write any outputs in a fragment shader. For the vertex shader, it still makes no sense to not write a position, so this should still be required.

RESOLUTION: For the fragment shader, there are no rules; either kill can be called or not, and if not, nothing need be written to, existing values are picked up from the pipeline. For the vertex shader, gl_Position should still be written, with the compiler giving a diagnostic when possible.

CLOSED on December 10th, 2002.

REOPENED on January 7, 2003, on concern of performance impact of writing default gl_FragColor and gl_FragDepth when a compiler thinks they are conditionally written. For color, it should just be undefined to not write gl_FragColor. For depth, it is more complex, as if depth is not written, then the fixed functionality computed depth should be used. However, if depth is conditionally written, the compiler will always have to initialize depth, which is a possible performance hit. Further discussion of this generated alternatives of always having to write gl_FragColor from a shader, or more complex things based on what the source code looks like.

RESOLUTION: Say that if a shader conditionally writes gl_FragDepth, then it must always write it.

See issue (95) for invariance concerns.

CLOSED January 17, 2003.

## #83 What should we do for modifying stencil? Does this effect push/pop state?

DISCUSSION: Writing a stencil value in a fragment shader introduces new functionality to OpenGL. By itself, it is of questionable use. On the other hand, existing operations like increment and decrement of stencil aren’t obviously expressible with the current specification. It may be that stencils should only be updated when a fragment shaders writes no outputs and the API has been set up for rendering to stencil. But, this has not been thoroughly investigated.

RESOLUTION: Stencil modifications are deferred to a future release.

CLOSED: December 17, 2002.

## #84 Should we add projective texture lookup? What about SHADOW textures?

DISCUSSION: Projective texture lookup could be postponed. Shadow textures are part of 1.4, so should be added, using something like textureShadow\*. However, if adding these, it’s trivial to also add projective lookup at the same time. What if shadow modes are not enabled? Expectation is the model looks like hardware just does whatever it’s set up to do by the application, and a shadow call from a shader picks up what was thus specified. Separately named functions will be used, because more data is input for the same target than for non-shadow lookups. Shadow could be called

"compare" instead of "shadow", but renderman calls it "shadow".

RESOLUTION: Add projective and shadow textures, with new names proj and shadow reflecting this. Furthermore, projective textures will accept two sizes of input: all will take a vec4, plus a 2D

projective will take a vec3, etc. Also ensure spec says results are undefined if using a shadow built-in on a texture not set up with a comparator, or using a non-shadow built-on on a texture set up with a comparator.

CLOSED January 17th, 2003.

## #85 How does the compiler know if it’s a 1D, 2D, 3D or CUBE texture that’s being accessed? Basing this on the number of components of an argument is error prone

DISCUSSION: Which texture to use was based on the type of the lookup coordinate argument.

However, it’s conceivable to support having many textures bound to the same texture unit number, and when the shader is written, the author should have in mind which one they are accessing, and they shouldn’t get the wrong one due to getting a type wrong. Explicitly saying which textue could be done with enums passed to the existing texture calls, or by adding new names with the texture type in the name. The problem with using enums is that implies an argument a variable could be passed into, while the requirement was to know at compile time. This argues for a name change.

RESOLUTION: Add more texture names so that it is explicit at compile time what kind of texture lookup is being done. Something like texture1D3, texture2D3, texture3D3, and textureCube3, where the last number is the number of components returned (could be 1, 2, 3, or 4).

CLOSED: December 17th, 2002.

## #86 There is little mention of color index in the spec. What support is provided for it?

DISCUSSION: Other extensions (ARB_vertex_program, ARB_fragment_program, texture application) say operations are undefined if rendering in COLOR INDEX mode.

RESOLUTION: Remove references to this other than saying COLOR INDEX operations are undefined.

CLOSED: December 10th, 2002.

## #87 Aux data buffers were part of the OGL2 white papers, but this specification needs to stand on the current OGL core

RESOLUTION: Remove aux data buffers from the specification. They can be added back if/when a future extension provides other buffers in core GL to write to.

CLOSED: December 17th, 2002.

## #88 Variable array indexes of some arrays may be difficult to implement

DISCUSSION: Arrays could be limited to just uniforms. However, this is restrictive, other graphics languages had local variable arrays. It also lacks orthogonality with structs that contain arrays, but may be used as a uniform or a local. It may help to limit the indexes of non-uniform arrays to be uniform indexes. It seems unlikely that a shader would initialize a whole non-uniform array and then index it with non-uniform indexes. (It seems much more likely that a non-uniform index would be applied to a static array or texture.) On the other hand, since this is unlikely, the language spec. could be left clean and full functionality, and for the next year or two it’s okay for compilers to complain that something is too complex (given that it’s an unlikely need).

RESOLUTION: Full array support will be specified.

CLOSED: January 7, 2003.

## #89 Why aren’t the varyings gl_TexCoord0...gl_TexCoordn an array?

DISCUSSION: There may be some performance or compiler convenience to knowing these at compile time or not allowing a variable index. This could interact with Issue (88), where if indices to varying arrays must be uniform then it’s easier to support these as arrays.

RESOLUTION: These will be changed to arrays. Working on details for the 2 problems listed above.

CLOSED: January 7, 2003.

REOPENED: Issue (88) was resolved with full array support. However, that leaves two possible issues with texture coords. i) not being able to tell how many coords are actually active in a shader, and ii) not being able to tell at compile time which coords are being accessed.

DISCUSSION: Alternatives:

1. Cg says something like this for constant loop-iteration: "Can be determined at compile time" is defined as follows: The loop-iteration expressions can be evaluated at compile time by use of intra-procedural constant propagation and folding, where the variables through which constant values are propagated do not appear as lvalues within any kind of control statement (if, for, or while) or ?: construct.
2. Be really restrictive: it must be a compile time constant or an induction variable with compile time constant start/end/increment values.
3. Actually solve the fundamental resource size problem: make the shader writer re-declare the array if they violate #2. That is, allow "varying gl_TexCoord[N]" to be declared by the shader, where N is how many coordinates they want to use and require it if #2 isn't satisfied. Allow full variable access in the language (with early hardware/compilers warning when it gets to tough.)
4. Do #3 with a #pragma instead.
5. Variation: Either all indices to gl_TexCoord[] in a shader must be constant expressions, or the shader must declare gl_TexCoord[] with a size. The built-in should be declared as an empty array so it’s consistent with C to declare it again with a size. Multiple modules can declare it with different sizes, the maximum will be used at link time.

RESOLUTION: Use #5.

CLOSED: January 24, 2003.

## #90 The built-ins specify texture functions that return integers. However, such textures are not in core OpenGL

RESOLUTION: These built-in functions will be removed. They can be added back as part of a future specification that adds integer textures to OpenGL.

CLOSED: December 17th, 2002.

## #91 There seems to be missing fog information in the fragment shader. gl_EyeZ is not enough. Should there be some derived information?

RESOLUTION: Expand float gl_EyeZ to vec4 gl_FogFragCoord.

CLOSED: December 17th, 2002.

## #92 We need a way to get object code back, just like the model of C on host processors

DISCUSSION: Lots in email. This is about lowest-level, machine specific code that may not even be portable within a family of cards from the same vendor. One main goal is to save compilation time. There seems to be general consensus that this has merit, but does not effect the language definition.

RESOLUTION: This is an API issue, not a language issue.

CLOSED on December 19, 2002 as being an API issue.

## #93 In reality, the output varying interface from a vertex shader is different than the input varying interface to a fragment shader, but this spec shares a single interface. This causes trouble with fog and lacks compatibility with mix & match of fixed functionality and ARB_fragment_program DISCUSSION: Splitting this interface in two makes sense. The fragment input should be compatible with ARB_fragment_program and the vertex output should be compatible with ARB_vertex_program. Fog can be dealt with this way, as reflected in the specification

RESOLUTION: This should be done.

CLOSED January 17, 2003.

## #94 The way the language spec. is today, it is not possible to know at compile time which targets are used on which units. If one believes hardware must be set up in advance with the right target on the right unit, this is a big problem

DISCUSSION: Possible solutions:

A. Use traditional OGL enables and binds and precedence to allow the app to communicate which target is the one that needs to be active when the shader runs.

B. Change the language to require compile-time inspection to be sufficient to see which target is being used on which unit.

C. Expect hardware to be able to dynamically access the requested target without having been set up in advance by the driver to do so.

D. Add a new entry point to the API for setting an active texture: a) BindTextureGL2(GLenum textureUnit, GLenum GLuint texture) (maybe without textureUnit parameter) But it seems that nobody wants such a function because of incompatibility to the standard pipeline. b) Another option may be a new utility function UseTexture(GLenum textureUnit, GLenum GLuint texture), which is completely equivalent to i) binding the texture, ii) enabling the target of this texture and iii) disabling all other targets. If texture is NULL, all targets are disabled. This solution would be 100% compatible to the standard pipeline; the new function is only for convinience, providing no new functionality (maybe as glu function).

E. Use ‘samplers’ the way Cg does.

RESOLUTION: Use samplers. Types sampler1D, sampler2D, sampler3D, and samplerCube will be added. They will not be writable in a shader. They can only be global uniforms. They can be arrays. They are opaque and cannot be operated on within a shader, only referenced. The first parameter of texture calls will accept them. The API must somehow bind texture or texture/unit to them. Enables, active-texture, etc. are superceded by these API bindings. At the language level, they will provide the basis for possible future virtualization of textures.

Also see Issue (97).

CLOSED January 17th, 2003.

## #95 Are there needs to solve invariance problems with gl_FragDepth or other aspects of the shading language?

DISCUSSION: Some have raised concerns here, but it’s not clear what the problems/solutions are.

1RESOLUTION: For gl_FragDepth, say that there is no guarantee of invariance between a fixed functionality depth and a shader computed depth. However, using the same shader depth computation multiple times in the same or different shaders will be invariant.

Defer other variance issues to the next release.

CLOSED January 17th, 2003.

## #96 The lod built-in doesn’t make sense for anisotropic filtering

DISCUSSION: Seems like we need lod1D, lod2D, lod3D, lodCube. But, the lod() built-in doesn't seem useful, given that the texture built-ins currently take biases, not absolute lod's. And also given that if anisotropic filtering were added, lod's specification would be troublesome.

RESOLUTION: Remove lod built-ins.

CLOSED January 17th, 2003.

## #97 Do we need a shadow sampler? E.g. a type sampler1DShadow, sampler2DShadow?

DISCUSSION: This gives even higher level of enforcement over hooking up the right texture state with the right built-in lookup function. On the other hand, it sets a direction for adding lots of type keywords.

RESOLUTION: Yes, add these types.

CLOSED January 24, 2003.

## #98 vec2, vec3, vec4 should be changed to float2, float3, float4 to be consistent with other languages. kill

should likewise be discard.

DISCUSSION: Should replace vec2, vec3, vec4 with float2, float3, float4 to match the established conventions of the Stanford RSL, Cg, and HLSL.

Additionaly, these names allow better support for data types based on other scalars (int2, int3, int4, half2, half3, double4, etc).

On the other hand, it seems consistent to have a scheme where a vector is made with a prefix type abbreviation (default is float), followed by "vec", followed by number of components.

| type    | vector of 4 |
| ------- | ----------- |
| float   | vec4        |
| int     | ivec4       |
| bool    | bvec4       |
| half    | hvec4       |
| double  | dvec4       |
| float16 | f16vec4     |
| int32   | i32vec4     |

Should we also change "kill" to "discard"? The rationale is that the OpenGL specification consistently uses the term "discard" when a fragment is discarded and never uses kill. The KIL

instruction does appear in both the ARB_fragment_program & NV_fragment_program specifications (probably because it makes a nice mnemonic for a three-letter instruction), but that's not consistent really with a core OpenGL specification. Another reason is that Cg/HLSL has "discard" be a keyword and there's no good reason to have redundant keywords for the same functionality.

RESOLUTION: Change kill to discard, leave vectors the way they are.

CLOSED January 24, 2003.

## #99 There is a mechanism lacking for dealing with a really large table in memory

DISCUSSION: Want to be able to efficiently communicate a large array of information to a vertex shader to support advanced animation techniques. This can be done today for arrays that fit into the

"uniform" space, but not for really large data structures. Need a way to declare these in the language, and need API support to initialize them. It can be argued that this functionality is mostly present through texture lookups. However, that doesn’t seem like the proper abstraction for a large array in memory.

RESOLUTION: Defer this to a future release. Include a specification as part of this issue.

CLOSED January 24, 2003

## #100 We have a name conflict with texture built-ins if in the future lod-bias forms are added to the vertex shader, or (more likely) absolute-lod forms are added to the fragment shader

RESOLUTION: Call bias forms the names we have now. Call absolute-lod forms the names we have now, suffixed with "Lod".

CLOSED January 24, 2003.

## #101 Add initialization of uniforms. For example, uniform vec3 Color = vec3(1.0, 1.0, 0.0); DISCUSSION: Has been requested by ISVs and NVIDIA. Doesn't seem necessary, but if majority wants this we can add it. Also need to consider doing this for samplers (but not for arrays?). Note in only makes sense for uniforms that are not changing during rendering, const globals are available, and textures cannot be specified, so this may be of less utility than some think. Still it has value

RESOLUTION: Do nothing. Save backward compatible enhancements for a future version of the language.

CLOSED: December 2003.

## #102 Fix arrays (multi-dimensional arrays, static initialization of array members, variable sized arrays, non-sized array parameters)

DISCUSSION: The function parameter declaration "vec4[ ]" does not say how big the array is, somewhat implying pass by reference. The calling conventions are pass by value. This syntax should be reserved for a possible future addition of passing an array by reference. To pass an array by value, one should say the size "vec4[5]". Unfortunately, this would prevent calling the same function with two differently sized arrays. But, then, perhaps, doing that should wait until there is a pass by reference mechanism for arrays. Static initialization could be done by using the constructor name of type followed by square brackets. E.g. "vec4[5] vArray = vec4[ ](v1, v2, v3, v4, v5);"

Once on this path, one could go further to making arrays first class objects. It could argue for declarations like "float[7] fArray", and allowing copying and comparing of arrays. This also implies eliminating auto-sizing of arrays, which is has other utility in making it easy to conserve interpolation resources. Going even further, multidimensional arrays could be added, and we'd have to consider arrays of arrays, typedefs, and true multidimensional arrays like "float[7,3] fMultiArray".

RESOLUTION: Be conservative with changes, but don't preclude backward compatibility of future additions of all this functionality. The minimal changes that allow future backward compatibility are: make function parameter declarations require the array size and make it a link error to have different shaders with different sizes for the same global array. This allows us to keep the existing auto-sizing The OpenGL Shading Language

1of gl_TexCoord. Array sizes have to match for a function call to be selected based on parameter types, otherwise there would be a "no matching overloaded function" error. The rest is deferred to a future release.

CLOSED: December 2003.

## #103 Clarify specification on vector matrix constructors initialized with fewer than needed elements. Do you drop last row/column or initialize elements in order? Users would expect the former. Also, limit constructors and make them operate in a more sensible manner

DISCUSSION: The spec says enough components have to be provided. There are missing interesting constructors for building matrices in other than column major order. There is also no upper bound today on how many parameters may be passed to a constructor in a valid program. We could also enumerate all the useful constructors, and say exactly what each one does. Also, there is some blurring between the use of a constructor as a type converter versus a type builder. This would be made more clear with a clean list of prototypes. The complete list was avoided earlier in favor of a simpler specification.

RESOLUTION: Limit the number of arguments to a constructor. The last useful argument can have more components than needed, and just be partially consumed, but arguments beyond that are disallowed. Also, prevent other constructions that are today useless, but in the future could be defined to be useful. These would be matrices constructed from other different sized matrices. E.g.

mat4(mat3) or mat2(mat4). Actual support of new kinds of constructors is deferred to a future release.

CLOSED December 2003.

## #104 Should there be a fast atan() etc?

DISCUSSION: Two different directions to go. One is adding a performance vs. accuracy trade-off.

The other is to add domain limited functions. Both could be supported, with new sets of names. Eg.

atanDom() would be a domain limited arc tangent.

atanFast() would be a lower precision, higher performance arc tangent.

RESOLUTION: Do it as an extension first.

CLOSED December 2003.

## #105 Change spec so that code can be generated by concatenation of all shader strings across shader objects

DISCUSSION: There is some utility in just waiting to link time to do full compilation and link in one step. One way to do this is to concatenate all the shaders together (within vertex or within fragment, not across), and parse them. They still have to be parsed at compile time, to return errors, etc., but would be parsed a second time at link time.

RESOLUTION: Not to do this. It is easy to make a specification error in making this change.

CLOSED December 2003.