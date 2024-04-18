如果没有游戏引擎，开发一个游戏需要从零开始写渲染的部分，每开发一个新游戏就要重新造一遍轮子。然而，不同游戏的渲染逻辑大同小异，无非是创造一个游戏场景、向场景添加物体，并为每个物体创造渲染材质、添加场景的全局信息（例如光照，相机）等等。游戏引擎的渲染模块将这些东西给抽象了出来，使得我们每次写游戏的渲染部分不需要重新造轮子。实际上这不仅仅是渲染模块的意义，这是整个游戏引擎存在的意义。

OpenGL, Vulkan, DirectX等图形接口的用处非常广泛，并不仅仅用于游戏渲染。游戏引擎的渲染模块对这些API做了些简单的封装和抽象。这些高级的抽象可以提高开发效率，开发者可以减少和GPU buffer或者GPU image打交道、帮助开发者进行GPU内存管理等等。

以Vulkan为例游戏引擎可以抽像一个RenderObject对象，该对象代表渲染游戏场景里的一个物体所需要的资源，因此我们渲染一个游戏场景，只需要为其中每一个物体都创造一个RenderObject的实例即可。

```c++
struct RenderObject {
	uint32_t indexCount;
	uint32_t firstIndex;
	VkBuffer indexBuffer;

	std::shared_ptr<GpuMaterialInstance> material;
  Bounds bounds;
	glm::mat4 transform;
	VkDeviceAddress vertexBufferAddress;
};
```

游戏引擎还可以抽象一些渲染游戏场景的函数，比如把渲染几何，渲染GUI，渲染背景的逻辑给区分开：

```c++
void DrawGeometry(VkImageView targetView, const DrawContext& context);
void DrawImgui(VkImageView targetView, VkExtent2D extent);
void DrawBackGround(VkImageView targetView, VkExtent2D extent);
```

开发一个游戏引擎的渲染模块并不等同于重新开发一套渲染API用来渲染游戏画面（理论上也可以，但我真觉得没必要，工作量太大了，大公司可以这样做，比如Unity或者Unreal)。开发者依然需要了解Vulkan API才能进行游戏渲染的开发，但是相比于从零开始写vulkan，游戏引擎为我们提供了很大的便利。