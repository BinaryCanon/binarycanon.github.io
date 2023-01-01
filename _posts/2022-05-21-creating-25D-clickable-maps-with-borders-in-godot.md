---
title: Creating 2.5D Clickable Map with Borders in Godot
date: 2022-05-21 00:00:00 +0900
categories: [Programming, Map]
tags: [interactive map]     # TAG names should always be lowercase
pin: true
image:
  path: /assets/img/posts/20220521/godot-workflow.jpg
---
# Collision Detection Using a Color Map

After some searching, I saw an interesting way to create a clickable map that seems efficient and composed of two maps. The first is visible without any collision detection (our wireframe), and the second is in front with a unique color for each country, and it has a collider. Then, we check the pixel color from the mouse's position; if it's pink, we know it's Australia. We can scale it down to countries, then regions, etc. Here's the example from <a href="https://gamedev.stackexchange.com/questions/92038/how-can-i-map-regions-on-a-world-map-image" target="_blank">StackExchange</a>:

![Countries map](/assets/img/posts/20220521/countries-map.png)
_Map of all countries_

![Color coded map](/assets/img/posts/20220521/color-coded-countries.png)
_Each region will have a unique color_

---

# Understanding the Workflow
![Godot workflow](/assets/img/posts/20220521/godot-workflow.jpg)
_Godot workflow_

I created the following workflow:
`TextureMap` and `CountriesWireframe` are simple 3DSprites. 
`HiddenColorData` is a QuadMesh I manually scaled to the size of the `TextureMap`, and it has a collider that will be used for `get_pixel()`.

<h2>Code Snippets</h2>
I encountered an excellent <a href="https://reddit.com/r/godot/comments/o8gqi5/godot_meets_grand_strategy_my_world_map_few_tips/" target="_balnk">post</a> on Reddit which already implemented what I wanted. I mainly used <a href="https://godotengine.org/asset-library/asset/127" target="_blank">the asset</a> the author highlighted for camera calculations with ray casting: we perform a ray cast from the camera to the point on the invisible (color) map, then it gets the pixel and we decide what this pixel means (which country was selected).

```gdscript
onready var _hidden_quad_color_data = $HiddenColorData

func _ready():
	# Load the color-coded map to the QuadMesh (HiddenQuadColorData) and lock it so we can pick its colors.
	var temp_texture = ImageTexture.new()
	var temp_image = Image.new()
	temp_image.load("res://Assets/Maps/Images/World Map/world_map_colors.png")
	temp_texture.create_from_image(temp_image)
	_hidden_color_map_ = temp_texture.get_data()
	_hidden_color_map_.lock()
```

Here are the maps, currently only Brazil has a color (green):

![Visible map](/assets/img/posts/20220521/world-map-blank.png)
_A visible map which will be shown to the player_

![Hidden map](/assets/img/posts/20220521/world-map-colors-coded.png)
_A hidden map with unique colors for each country. Only Brazil for now_

<h3>Camera movement:</h3>

```gdscript
func _physics_process(delta):
	# Define -1 to +1 direction changes
	cam_direction.x = (-int(Input.is_action_pressed("map_left")) + int(Input.is_action_pressed("map_right")))
	cam_direction.y = (-int(Input.is_action_pressed("map_down")) + int(Input.is_action_pressed("map_up")))
	cam_direction.z = (-int(Input.is_action_pressed("map_zoom_in")) + int(Input.is_action_pressed("map_zoom_out")))
	cam_direction = cam_direction.normalized()
	
	# Interpolate velocity and modify cam position
	cam_velocity = cam_velocity.linear_interpolate(cam_direction * cam_speed, cam_acceleration * delta)
	_camera.transform.origin += cam_velocity
```

<h3>Handling hover and click event:</h3>

```gdscript
func _unhandled_input(event):
	# Handling both hover and click
	# InputEventMouseButton & InputEventMouseMotion different logic!
	if event is InputEventMouseButton or event is InputEventMouseMotion:
		if _hidden_color_map_ == null: return false
		# Get mesh size to detect edges and make conversions
		# This code only support PlaneMesh and QuadMesh
		var quad_mesh_size = _hidden_quad_color_data.mesh.size
		
		# Find mouse position in Area
		var from = _camera.project_ray_origin(event.global_position)
		var dist = 100
		var to = from + _camera.project_ray_normal(event.global_position) * dist
		var result = get_world().direct_space_state.intersect_ray(from, to, [], _hidden_quad_color_data.get_child(0).collision_layer,false,true)
		var mouse_pos3D = null
		if result.size() > 0: mouse_pos3D = result.position
		# Check if the mouse is outside of bounds, use last position to avoid errors
		# NOTE: mouse_exited signal was unreliable in this situation
		var is_mouse_inside = (mouse_pos3D != null)
		if is_mouse_inside:
			# Convert click_pos from world coordinate space to a coordinate space relative to the Area node.
			# NOTE: affine_inverse accounts for the Area node's scale, rotation, and translation in the scene!
			mouse_pos3D = _hidden_quad_color_data.get_child(0).global_transform.affine_inverse() * mouse_pos3D
			_last_mouse_pos3D = mouse_pos3D
		else:
			mouse_pos3D = _last_mouse_pos3D
			if mouse_pos3D == null:
				mouse_pos3D = Vector3.ZERO

		# convert the relative event position from 3D to 2D
		var mouse_pos2D = Vector2(mouse_pos3D.x, -mouse_pos3D.y)
		# Right now the event position's range is the following: (-quad_size/2) -> (quad_size/2)
		# We need to convert it into the following range: 0 -> quad_size
		mouse_pos2D.x += quad_mesh_size.x / 2.0
		mouse_pos2D.y += quad_mesh_size.y / 2.0
		# Then we need to convert it into the following range: 0 -> 1
		mouse_pos2D.x = mouse_pos2D.x / (quad_mesh_size.x)
		mouse_pos2D.y = mouse_pos2D.y / (quad_mesh_size.y)
		# Finally, we convert the position to the following range: 0 -> _hidden_color_map_.size
		mouse_pos2D.x = mouse_pos2D.x * _hidden_color_map_.get_width()	
		mouse_pos2D.y = mouse_pos2D.y * _hidden_color_map_.get_height()
		
		# Detect country color code
		var px_color = _hidden_color_map_.get_pixelv(mouse_pos2D)
		
		# Decide what to do
		if event is InputEventMouseButton:
			mouse_button_country_handler(px_color)
		elif event is InputEventMouseMotion:
			mouse_motion_country_handler(px_color)
```

By the time of writing this post, I already created a shader to select countries (I'll write about it next). The code below refers to that shader:
```gdscript
_current_selected_country = null  # Should be at the top of your script

# If the country clicked, create a border. 
func mouse_button_country_handler(country_color_code):
  if country_color_code == Color(0,1,0) \  # Green color
	and _current_selected_country != "Brazil":
		_brazil.material_override.set_shader_param("IsOutlined", true)
		# Store locally the current selected country 
		_current_selected_country = "Brazil"
	elif country_color_code != Color(0,1,0):  # Green color
		# Reset selection effect
		_current_selected_country = null
		_brazil.material_override.set_shader_param("IsOutlined", false)
		_brazil.material_override.set_shader_param("ColorAlbedoUniform", Color.black)


# If the country hovered, highlight it gently. 
func mouse_motion_country_handler(country_color_code):
	if country_color_code == Color(0,1,0) \  # Green color
	and _current_selected_country != "Brazil":
		_brazil.material_override.set_shader_param("ColorAlbedoUniform", Color.red)
	elif _current_selected_country != "Brazil":
		# Reset hover effect
		_brazil.material_override.set_shader_param("ColorAlbedoUniform", Color.black)		
		
```

> Any code related to the camera is a combination of the asset mentioned above and the code made by the author of the reddit post - <a href="https://github.com/rafgro/godot25dmap/blob/master/camera.gd" target="_blank">link to his repo</a>. 
{: .prompt-info }

> If you get a side-effect of non-consistent layers' locations when moving the camera, make sure you only translate the parent node - any child's translations should set to 0.
{: .prompt-tip }
