---
layout: post
title: "블렌더 스크립트를 이용한 간단한 모델링과 렌더링"
date: 2021-05-20 16:00:00
categories: dev
tags: Blender 3D
cover: "/assets/21_05_Blender/21_05_Blender.png"
---

<img src="/assets/21_05_Blender/21_05_Blender.png" width="" alt="Blender">

안녕하세요. 3D 그래픽스팀 최윤정입니다. 어반베이스에서는 블렌더를 이용한 렌더링 관련 업무를 맡고 있습니다.

'블렌더'는 Maya나 3D Max와 같은 3D 모델링 소프트웨어 입니다. GUI를 통해 디테일한 모델링이 가능하지만 블렌더에서 제공하는 API를 사용해 파이썬 스크립트를 작성하면 블렌더의 기능을 사용할 수도 있습니다. 블렌더 API를 사용하여 블렌더 애드온을 개발할 수도 있고, GUI를 제공하지 않는 환경에서 스크립트를 통해 모델링 및 렌더링 결과를 저장하는 것도 가능합니다.

이번 포스팅에서는 간단한 모델링과 모델링 결과를 렌더링하여 저장하는 과정을 스크립트로 작성하면서 블렌더 API의 간단한 사용 방법에 대해 다루어보려 합니다.

<br>

# 초기 구성
저는 Blender 2.91.2 버전을 사용하였습니다.
블렌더의 "Scripting" 탭에서 스크립트를 작성할 수는 있는데 단순한 메모장과 비슷한 수준의 기능만 제공하기 때문에 블렌더 내에서 스크립트를 작성하는 것이 저는 매우 불편하더라고요.

그래서 VSCode, Notepad++와 같은 다른 에디터에서 스크립트를 작성하고 해당 스크립트를 블렌더 스크립팅 레이아웃에 복사/붙여넣기 하는 방식으로 개발했는데요. 훨씬 더 편했습니다.
목표는 스크립트 실행만으로 다음과 같은 이미지를 저장하는 것입니다.

<img src="/assets/21_05_Blender/Goal.png" width="" alt="">

우선 아래와 같은 코드를 작성하여 스크립트 실행 시 필요한 모듈을 포함시킵니다.
```python
import bpy
from mathutils import Vector
from math import inf, radians
import os
```

여기서 블렌더에서 제공하는 모듈은 bpy와 mathutils입니다. mathutils는 블렌더에서 제공하는 수학 관련 기능을 제공하는 모듈입니다. 블렌더 자체에 대한 기능이라기 보다는 그래픽스에서 많이 사용되는 Vector, Matrix, Quaternion 에 대한 자료구조를 제공하여 코드를 통해 모델링을 하거나, 모델을 조작하는데 유용한 모듈입니다. bpy는 블렌더의 기본적인 API를 제공하는 모듈입니다. 

저희가 사용하는 서브모듈들은 다음과 같습니다.

-bpy.data
--블렌더 프로그램에서 모델링한 것을 저장할 때, .blend 파일에 저장되는 데이터들을 다루는 모듈 --점/선/면으로 정의된 mesh, 색/거칠기/매끄러움 등 물체의 재질을 표현하는 material, mesh/material/texture 등의 정보를 포함하는 하나의 물체 자체를 표현하는 object 등을 포함

-bpy.context
--현재 블렌더의 컨텍스트에 대한 데이터들(활성화된 창에 대한 데이터들)을 다루는 모듈 --선택된 object들이나 현재 scene과 같은 데이터

-bpy.ops
--사용자가 GUI 상에서 블렌더와 상호작용하는 모든 행위에 대한 operation을 다루는 모듈 --object를 조작하는 행위들을 bpy.ops를 통해 스크립트에서 구현이 가능

제가 처음 API를 사용할 때, bpy.data나 bpy.context는 블렌더 공식 문서를 많이 참고했는데 bpy.ops는 Scripting탭 좌하단에 있는 info 레이아웃에 출력된 로그를 통해 확인했습니다.

<img src="/assets/21_05_Blender/info_ops.PNG" width="" alt="">

위 사진처럼 info 레이아웃에 사용자가 입력한 내용들이 로그로 찍히게 되는데, 저는 GUI에서 특정 행동을 하고 로그를 통해 해당 행동에 해당하는 bpy.ops의 함수 정보를 얻어서 스크립트 작성하면서 API를 익혔습니다.

<br>

# 뼈대 작성
스크립트 작성을 하는 각 단계를 함수로 나누어 각 함수를 구현하고 설명하는 방식으로 진행하겠습니다. 다음과 같이 뼈대를 작성합니다.

```python
def reset_blender_data():
    pass

def create_cube(location, scale):
    pass

def set_object_color_rgba(blender_object, color):
    pass

def get_boundary_info(blender_object):
    pass

def create_floor_plane(boundary_info):
    pass

def create_camera(location, target_vector):
    pass

def create_sunlight():
    pass

def save_image_with_gpu(resolution, file_path, file_name):
    pass
```

각 함수의 역할은 다음과 같습니다.
- reset_blender_data
-- 스크립트 실행 전에 불필요한 데이터들을 삭제하는 함수
- create_cube
-- 인자로 받은 위치와 사이즈를 통해 직육면체를 생성하고 배치하는 함수
- set_object_color_rgba
-- 블렌더 object의 색을 설정하는 함수
- get_boundary_info
-- 블렌더 object를 감싸는 bounding box의 정보를 계산하여 반환하는 함수
- create_floor_plane
-- 블렌더 object 밑에 바닥면을 생성하는 함수
- create_camera
-- 특정 물체를 바라보는 카메라 object를 생성하는 함수
- create_sunlight
-- 태양광에 해당하는 조명 object를 생성하는 함수
- save_image_with_gpu
-- 렌더링에 사용할 엔진 설정
-- 해상도 설정
-- 인자로 받은 경로에 렌더링 결과 사진을 저장

<br>

# reset_blender_data 함수 
다음과 같이 구현합니다
```python
def reset_blender_data():
    for bpy_data_iter in (
            bpy.data.objects,
            bpy.data.meshes,
            bpy.data.lights,
            bpy.data.cameras,
            bpy.data.materials
    ):
        for id_data in bpy_data_iter:
            bpy_data_iter.remove(id_data)
```

bpy.data에서 object, meshes, lights, cameras, material와 관련된 데이터들을 모두 삭제하도록 구현하였습니다. 필요에 따라 추가적으로 삭제하고 싶은 대상(ex. images, collection)이 있는 경우, 첫번째 반복문의 튜플 안에 추가하시면 됩니다.

<br>

# create_cube 함수 
다음과 같이 구현합니다
```python
def create_cube(location, scale):
    bpy.ops.mesh.primitive_cube_add(size=1.0)
    cube_object = bpy.context.active_object
    cube_object.name = "cube"
    cube_object.location = location
    cube_object.scale = scale
    return cube_object
```
매개변수인 location과 scale은 (x,y,z)축에 대한 정보를 tuple 형식으로 저장한 변수입니다. 이번 포스팅에서는 두 가지 방식으로 blender object를 생성하는데 그 중 하나가 bpy.ops를 사용하는 것입니다. 

bpy.ops.mesh.primitive_cube_add는 기본적인 정육면체를 생성해주는 함수입니다. 이를 통해 현재 씬에 정육면체가 생성됩니다.

bpy.ops를 통해 object를 생성하게 되면 생성된 object가 자동으로 선택되며 활성화됩니다. 그래서 bpy.context.active_object를 통해 생성된 정육면체를 변수(cube_object)로 저장할 수 있습니다.

location과 scale은 각각 그래픽스의 transform에서 위치와 크기를 나타내는 bpy.types.Object의 속성이고, cube_object는 bpy.types.Object의 인스턴스입니다. 인자로 받은 location과 scale로 cube_object의 transform을 설정한 뒤, 해당 객체를 다시 사용하기 위해 반환합니다.

<br>

# set_object_color_rgba 함수
다음과 같이 구현합니다.
```python
def set_object_color_rgba(blender_object, color):
    material = bpy.data.materials.new(name=blender_object.name)
    material.use_nodes = True

    principled_bsdf_node = material.node_tree.nodes['Principled BSDF']
    principled_bsdf_node.inputs['Base Color'].default_value = color

    blender_object.data.materials.append(material)
```
매개변수는 색상을 입힐 blender object와 입힐 색상의 rgba 정보를 tuple 형식의 color 변수입니다. object에 색상을 입히기 위해서는 material 정보가 필요하기 때문에 bpy.data.materials.new를 사용하여 새로운 material을 생성합니다.

해당 material을 렌더링하는데 사용할 셰이더 노드로 설정하기 위해 use_node 속성값을 True로 해줍니다. 여기서 셰이더 노드는 프로그램 Shading 탭에서 볼 수 있는 노드를 뜻합니다.

기본적으로 material을 생성하게 되면, 재질 정보를 설정하는 "Principled BSDF" 노드와 셰이딩 대상(surface/volume)을 설정하는 "Material Ouput" 노드가 있습니다.

저희는 큐브의 표면에 색상을 입히려 하기 때문에 "Principled BSDF" 노드의 기본값을 인자로 받은 color로 설정합니다.

마지막으로 색상을 입힐 블렌더 object의 재질 속성에 지금까지 만들고 설정한 material을 연결해주면 끝납니다.

<br>

# get_boundary_info 함수
다음과 같이 구현합니다.
```python
def get_boundary_info(blender_object):
    bpy.context.view_layer.update()

    min_point = [inf, inf, inf]
    max_point = [-inf, -inf, -inf]

    boundary_points = [blender_object.matrix_world @
                       Vector(point) for point in blender_object.bound_box]
    for point in boundary_points:
        for i in range(3):
            if point[i] > max_point[i]:
                max_point[i] = point[i]
            if point[i] < min_point[i]:
                min_point[i] = point[i]

    center_point = []
    boundary_length = []
    for i in range(3):
        center_point.append((min_point[i]+max_point[i])/2)
        boundary_length.append(max_point[i]-min_point[i])

    boundary_info = {
        "min_point": min_point,
        "max_point": max_point,
        "center_point": center_point,
        "boundary_length": boundary_length
    }

    return boundary_info
```
생성한 큐브 밑에 바닥면을 생성할 때 필요한 정보를 구하기 위해 만든 함수입니다. 단순히 큐브 밑에 바닥면을 생성하는 경우, 큐브 object의 location과 scale 값을 이용해 계산을 하여 생성할 수도 있지만, 좀 더 일반적인 상황에 대응할 수 있도록 bounding box의 정보를 계산하는 함수를 구현하였습니다.

블렌더는 object의 bound_box 속성에서 해당 object를 감싸는 bounding box의 8개 정점에 대한 정보를 제공합니다. 하지만 이 정보들은 모델 좌표계 기준의 좌표이기 때문에 이를 월드 좌표계로의 변환이 필요합니다.

모델 좌표계를 월드 좌표계로 변환해주는 월드 변환 행결은 object의 matrix_world 속성을 통해 알 수 있습니다. 블렌더에서는 효율성을 위해 object의 transform이 변경되어도 바로 matrix_world 속성을 다시 계산하여 갱신하지 않습니다. 따라서 사용자는 bpy.context.view_layer.update()를 호출하여 갱신을 요청해야 matrix_world 속성이 갱신됩니다.

갱신한 matrix_world에 bounding box의 각 정점을 곱하면 해당 정점의 월드 좌표계를 구할 수 있습니다. @는 mathutils에서 제공하는 연산으로 행렬 및 벡터의 곱하기 연산을 의미합니다.
정점들의 월드 좌표계를 이용하여 최소값, 최대값, 중점, 길이를 계산하면 해당 정보들을 딕셔너리로 반환해줍니다.

<br>

# create_floor_plane 함수
다음과 같이 구현합니다.
```python
def create_floor_plane(blender_object):
    boundary_info = get_boundary_info(blender_object)
    min_point = boundary_info["min_point"]
    center_point = boundary_info["center_point"]

    plane_position = (center_point[0], center_point[1], min_point[2])
    plane_scale = (20, 20, 1)

    bpy.ops.mesh.primitive_plane_add()
    plane_object = bpy.context.active_object
    plane_object.name = "floor plane"
    plane_object.location = plane_position
    plane_object.scale = plane_scale
```
위에서 계산한 큐브의 경계 정보를 이용하여 바닥면을 생성합니다.
바닥면 생성 과정 자체는 큐브를 생성하는 create_cube 함수와 거의 똑같습니다. 큐브의 경우 사용자가 위치 및 크기를 설정해주고, 바닥면의 경우 object를 인자로 받아 object의 바닥에 면을 생성해줍니다.

바닥면의 x,y좌표는 object의 중심의 x,y좌표와 일치시켜주고, z좌표는 object의 z좌표 중 가장 작은 값으로 설정해 줍니다.
scale의 경우 카메라 화면에 꽉 차도록 제가 임의로 설정하였으나  이 부분 또한 object의 크기나 카메라의 위치 등을 고려하여 개선할 수 있습니다.

<br>

# create_camera 함수
다음과 같이 구현합니다.
```python
def create_camera(location, target_vector):
    camera_data = bpy.data.cameras.new("Main Camera")
    camera_data.lens = 50

    camera_object = bpy.data.objects.new("Main Camera", camera_data)
    camera_object.location = location

    scene = bpy.context.scene
    scene.collection.objects.link(camera_object)
    scene.camera = camera_object

    # lookAt the target point
    if not isinstance(target_vector, Vector):
        target_vector = Vector(target_vector)
	
	camera_location = camera_object.location
    direction = target_vector - location
    quat = direction.to_track_quat('-Z', 'Y')
    camera_object.rotation_euler = quat.to_euler()

    return camera_object
```
create_cube 함수 구현 부분에서 언급했듯이, 두가지 방식으로 blender object를 생성하는데 그 중 두 번째 방식으로 카메라 object를 생성합니다.

우선 bpy.data.cameras.new 함수를 사용하여 카메라 데이터를 생성합니다. 카메라 데이터는 카메라 시점에 대한 데이터들을 가지고 있는데, 투영 방법(평행, 원근)이나 가시 부피과 관련된 데이터들이 있습니다.

bpy.data.objects.new 함수를 사용하여 생성한 카메라 데이터를 지니는 blender object를 생성합니다. 이 object를 카메라 객체라고 부르겠습니다.

카메라 객체의 location 속성을 인자로 받은 location으로 설정하여 카메라 객체의 위치를 설정합니다. 생성한 카메라 객체를 현재 씬에 추가하고 씬의 메인 카메라로 설정해야 합니다.

bpy.context.scene는 현재 씬을 가리키는데, 현재 씬에 대해서 collection.objects.link 메소드를 통해 생성한 카메라 객체를 추가해줍니다. 그리고 현재 씬의 camera 속성을 카메라 객체로 설정해주면 씬의 메인 카메라가 설정됩니다.

마지막으로 카메라가 target_vector를 향하도록 회전을 해주어야 합니다. 카메라가 바라 보는 방향인 direction을 계산해야 하는데, mathutils의 Vector를 사용하기 위해 튜플형의 location 변수를 그대로 사용하지 않고, camera_object.location를 direction 계산 시에 사용합니다.

카메라가 생성되면 디폴트 값으로 -Z축을 바라보며 up-vector는 Y축 방향이 됩니다. 그래서 카메라가 바라보아야 하는 direction에 대해서 to_track_quat('-Z', 'Y') 함수를 이용하면 카메라 transform의 회전값을 구할 수 있습니다.

이 회전값은 쿼터니안 각이기 때문에, 오일러 각을 사용하여 카메라의 회전값을 설정하기 위하여 to_euler()를 통해 쿼터니안을 오일러로 변환해 각도를 설정합니다.

<br>

# create_sunlight 함수
다음과 같이 구현합니다.
```python
def create_sunlight():
    light_data = bpy.data.lights.new(name="sun", type="SUN")
    light_data.energy = 4.5

    light_object = bpy.data.objects.new(
        name="sun", object_data=light_data
    )
    light_object.rotation_euler = (radians(20), radians(30), radians(-20))

    scene = bpy.context.scene
    scene.collection.objects.link(light_object)

    return light_object
```
마지막으로 생성해야 하는 object는 빛입니다. 여기서는 방향성 광원에 해당하는 태양광을 생성합니다. 광원 object를 생성하는 것은 카메라 object를 생성하는 것과 비슷하게 구현됩니다. 

광원 데이터를 생성하기 위해 bpy.data.lights.new 함수를 호출하는데, type 인자에 POINT/SUN/SPOT/AREA 를 문자열로 넘겨주어 다양한 광원 형태로 광원 데이터를 생성할 수 있습니다.

광원 형태에 따라 설정할 수 있는 광원 데이터가 달라집니다. 예를 들어 면적 광원의 경우 면의 모양(정사각형/직사각형/원) 및 면의 크기 등을 설정할 수 있습니다.

여기서는 간단하게 빛의 세기만을 설정합니다.광원 object를 만드는 과정은 카메라 object를를 만드는 것과 똑같습니다. 다만 방향성 광원의 경우 광원의 방향만 중요하기 때문에 위치 대신 회전값을 설정하였습니다.

<br>

# save_image_with_gpu 함수
다음과 같이 구현합니다.
```python
def save_image_with_gpu(resolution, file_path, file_name):
    scene = bpy.context.scene

    scene.render.resolution_x = resolution
    scene.render.resolution_y = resolution

    scene.render.engine = 'CYCLES'
    scene.cycles.device = 'GPU'
    scene.cycles.denoiser = 'NLM'
    scene.cycles.use_denoising = True

    # set Cycles add-on
    cycles_preferences = bpy.context.preferences.addons['cycles'].preferences
    cycles_preferences.compute_device_type = "CUDA"

    for devices in cycles_preferences.get_devices():
        for device in devices:
            if device.type == "CUDA":
                device.use = True
            else:
                device.use = False

    if not os.path.exists(file_path):
        os.makedirs(file_path)
	
	# output setting
    scene.render.image_settings.file_format='PNG'
    scene.render.filepath = os.path.join(file_path, file_name)
	
	# rendering
    bpy.ops.render.render(write_still=True)
```
위에서 구현한 함수들로 씬을 구성한 후 해당 씬을 렌더링하고 결과 이미지를 저장하는 함수입니다. 여기서는 Cycles 엔진을 사용하여 렌더링하며, Nvidia GPU가 있다는 가정하에 gpu를 이용하여 렌더링 속도를 빠르게 하였습니다.

렌더링과 관련된 설정은 현재 씬에 대한 설정으로 bpy.context.scene에서 접근합니다. 크게 일반적인 설정을 하는 scene.render과 Cycles 엔진 설정을 하는 scene.cycles로 나눌 수 있습니다.

해상도에 해당하는 x축 픽셀수와 y축 픽셀수는 scene.render에서 설정하는데 여기서는 정사각형 형태로 설정하였습니다. 그리고 렌더링 엔진을 Cycles로 설정합니다. scene.cycles에서는 GPU 설정 및 잡음 제거를 위한 설정을 해줍니다. 

그리고 GPU 사용 설정을 위해서는 추가적으로 Cycles 애드온도 설정이 필요하기 때문에, bpy.context.preferences.addons['cycles'].preferences를 통해 애드온 설정을 따로 해주었습니다.

scene.render.image_settings 에서는 저장할 아웃풋 이미지의 포멧 및 압축률과 같은 설정을 할 수 있는데 여기서는 단순히 png 형식으로 저장하도록 설정하였습니다.

scene.render에서 저장할 디렉토리를 설정하고, 마지막으로 bpy.ops.render.render를 통해 렌더링 및 위에서 설정한 형식으로 결과물을 저장합니다.

<br>

# main 함수 구현
마지막으로 지금까지 구현한 함수들을 이용하여 씬을 구성하고 렌더링하는 메인 함수를 다음과 같이 작성합니다.
``` python
if __name__ == "__main__":
    reset_blender_data()

    cube_loc = (0, 0, 0)
    cube_size = (2, 1, 3)
    cube_color = (0.8, 0.35, 0.29, 1.0)
    cube_obj = create_cube(cube_loc, cube_size)
    set_object_color_rgba(cube_obj, cube_color)

    create_floor_plane(cube_obj)

    camera_loc = (4.5, 6, 5)
    create_camera(camera_loc, cube_loc)

    create_sunlight()

    save_image_with_gpu(1024, "D://", "Test")
```
save_image_with_gpu에서 두번째 및 세번째 인자에는 자신이 원하는 경로를 넣어주시면 됩니다. 스크립트를 직접 블렌더 프로그램 상에서 돌릴 수도 있지만  블렌더를 실행하지 않은 상태에서 CLI를 통해 실행시킬 수 있습니다.

윈도우의 경우, CMD 창에서 경로를 blende.exe 파일이 있는 디렉토리로 설정하고 다음과 같은 명령어를 사용합니다.
```
blender --background --python <스크립트 절대 경로>
```
예를 들어, 위 스크립트 이름이 test.py이고 D:\에 있다고 한다면  다음과 같은 명령어로 스크립트를 실행시킬 수 있습니다.
```
blender --background --python D:\test.py
```
<br>

# 마치며
제가 처음 이 업무를 담당했을 때, 블렌더 API가 익숙하지가 않아 스크립트를 작성하는데 어려움이 있었는데요. 이 포스팅이 블렌더 스크립트 작성을 이제 막 시작하시는 분들께 도움이 되었으면 좋겠습니다. 감사합니다.  

