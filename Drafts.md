# Drafts

## Metal

*26 Nov 2025*

> To render to the view, create an MTLRenderPassDescriptor object that targets a texture provided by the layer.

[Creating a custom Metal view](https://developer.apple.com/documentation/Metal/creating-a-custom-metal-view)

Metal draws into a texture. A texture is a memory block with pixel data. The texture Metal draws into is either provided by the view, or created manually by the user (allocating the memory block), so we get a reference to it and use it, for example to retrieve its content and store it on disc, which is what I did in the SKRenderer app sample.