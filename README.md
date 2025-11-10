# Voxel Plugin • Biomes
Assets for creating procedural terrain with biomes using Voxel Plugin 2.0

   Tutorial:

   Installing
Extract the contents of the 'Biomes Plugin.zip' directly into your project's 'Plugins' folder.

In the top left of the Unreal Editor, navigate to Edit→ Plugins→ Installed→ Landscape→ enable: Voxel Plugin • Biomes

Restart your Unreal Engine



Getting started in an empty level:

	Using the provided Terrain and Biome Generators:
	
	Height Worlds (2D):
		#1. Add a Voxel World to your level, select 'VMM_Terrain' as its Mega Material asset, and 'VLS_Terrain' as the target Layerstack to render.
		
		#2. Add a Height Stamp to your level with the 'TerrainGenerator' asset as its graph input.
		
		#3. Add a Volume Stamp to your level with the 'BiomeGenerator' asset as its graph input.
		
		#4. Begin customising your Terrain and Biomes.
		
		
	Planetary Worlds:
		#1. Add a Voxel World to your level, select 'VMM_Terrain' as its Mega Material asset, and 'VLS_Terrain' as the target Layerstack to render.
		
		#2. Add a Volume Stamp to your level with the 'PlanetaryTerrainGenerator' asset as its graph input.
		
		#3. Add a Volume Stamp to your level with the 'PlanetaryBiomeGenerator' asset as its graph input.
		
		#4. Begin customising your Terrain and Biomes.
		
		
	
Creating Biome Types:

		#1. Create a Smart Surface, open it, and use the 'BiomeType' Surface Graph asset as its configuration parent. (use 'PlanetaryBiomeType' for planets)
		#2. Choose your Biome's materials and their placement ranges, and apply the asset to your Terrain through painting, stamps, or graphs as if it were a regular material.
		
NOTE:
If you're creating Biome Types for use on pre-existing terrains/graphs outside of this asset pack, you'll need to give it a proper base layer for the surface math to work with.
The input for this can be found in the Config category of the Biome Type asset.
	
	Heightworld Biomes only require a Height Layer.
	Planetary Biomes require a Volume Layer, their core's location at (0,0,0) coordinate, and at some point need to be told the planet's radius via metadata outputs in your Voxel Graphs. (Reference the 'PlanetaryTerrainGenerator' → 'PlanetaryBiomeGenerator' workflow for this)
	
	
		
Layerstack Intentions:

	Layerstack: 'VLS_Terrain'
	Layers:
			2D Height Layers
				00_Terrain_Height_Original (2D Natural/Base Terrain Generation)
				
				01_Terrain_Height_Base (2D Overrides to the Natural/Base Terrain Generation) ⭐ This is the target base height layer for Heightworld Biome Types.
				
				02_Terrain_Height_Edits (2D Runtime/Final Overrides, Decorations, and Excavations through the surface)
			
			3D Volume Layers
				03_Terrain_Volume_Original (Natural/Base Volumetric Generation)
				
				04_Terrain_Volume_Base (Overrides to the Natural/Base Volumetric Generation) ⭐ This is the target base volume layer for Planetary Biome Types.
				
				05_Terrain_Biomes (Applying Biome Types/Metadata overrides)
				
				06_Terrain_Stamps (Additional Overrides, Decorations, and Excavations through the surface)
				
				07_Terrain_Volume_Edits (Runtime/Final Overrides, Decorations, and Excavations through the surface)
				
				
			

Climates (Reworked):
	Climate Regions within Biome Types have been removed. It was expensive and overwhelming to interact with. Replaced with a Biome Generator template graph that blends between Biome Type assets, and an additional Snow Layer that generates ontop of all Biome Types based on Temperature, and a desired metadata snow additive.


		Metadata Asset: 'Temperature'
			0.0 = Cold Climate
			0.5 = Neutral Climate
			1.0 = Hot Climate
		Temperature can be viewed in the form of a thermal heatmap by applying the 'Temperature Viewer' graph to your level.

	
		Metadata Asset: 'Snow'
			0.0 = Snow will be removed, regardless of generation by Temperature.
			1.0 = Snow will be allowed to generate if the Temperature is less than or equal to (0.0).
			2.0 = Snow will be generated, regardless of the Temperature.
				This can be used to "melt" or place snow in desired areas without altering the world's temperature metadata.

Toggling Snow in a Biome Type

		'Use Snow Layer?' boolean: 
			False = No Snow Layer regardless of Temperature/Snow metadata | True = Enables Snow Layer generation via Temperature/Snow metadata

		'Snow Allowance' float:
			Snow generation via Temperature can be toggled in each Biome Type by scaling this variable.
			0.0 = Snow will be denied generation on the surface, but can still be applied by stamping/painting a Snow metadata value of (2.0)
			1.0 = Snow will be allowed to generate on the surface if the Temperature metadata value = (0.0)

		


Surface Depth Layers (Reworked):

Surface Layers have been slightly redone, with individual depth multipliers for each LOD, and the Topsoil Layer can now be cleared/placed via metadata similar to the Snow Layer.
	
	
		Metadata Asset: 'Topsoil'
			0.0 = Topsoils will be denied generation
			1.0 = Topsoils will be allowed to generate

			This is a simple, non-destructive way to create clearings and paths in Biomes without the need to select different dirt types for every edit operation.
			Just stamp/paint a Topsoil metadata value of (0.0), and the biome-unique Subsoil will be revealed.
				NOTE:
				If you're attempting to stamp/paint Topsoil clearings in an area where it's cold enough for snowfall and aren't seeing the surface change, it's likely that the Snow Layer is covering it, and you'll need to apply a 'Snow' metadata value of 0.0 to clear it from the surface as well.


	🌭 Bonus Asset: 
		Included is a simple Campfire blueprint that utilizes this concept.
		Place it on your Terrain and it'll remove Topsoils and Snow from the surrounding area.


The LOD Depth Multiplier Arrays:
		Array Element Index = LOD Index
		Array Element Value = The amount that we want to multiply the depth of the surface Layer for that LOD Index

			This is to fix a spotting artifact between Surface Depth Layers on distant LODs.
			This is caused by the LOD vertices of the voxel mesh being interpolated to a position lower/higher than the intended depth of the surface layer.
			These values are unique to each Biome Type, so if you're coming across this artifact and are trying to remedy it without seeing any visual changes, make sure you're adjusting values in the right Biome Type asset.
			


The 'StoneType' Surfaces (New):

A Smart Surface with a recursive template for stacking ore layers using an array of structures.
With it, you can create custom Stone Types with unique ore generation values that can be easily applied anywhere via biome-slots, stamps, graphs, and painting
This is a good way to easily turn rocks/boulders/mountains into a unique point-of-interest for mining with their own ore/stone generation properties by simply applying the custom Stone Type to surface slots.
		
		
		How to use:
			Create a Voxel Smart Surface with the config parent being the 'StoneType' surface graph.
			Inside your new Stone Type, choose your stone/ore materials, adjust their generation configs, and apply anywhere there's a Voxel Surface Type slot.
				NOTE:
				If you want to debug the size of your Ore Type slots with a "Minecraft X-Ray Mod" style view, drag the 'Manual_Ore_Generator' volume graph into your level.
				Adjust the values inside the 'Ore Generation Data' structure, copy it, and paste it into any Ore Type slot of your Stone Type.
	



Generating/Removing Trees using metadata and the 'PCGG_Forests' asset:

		Metadata Asset: 'Forests'
			0.0 = Remove Trees (Outside of Forest Biome)
			1.0 = Add Trees (Inside of Forest Biome)
