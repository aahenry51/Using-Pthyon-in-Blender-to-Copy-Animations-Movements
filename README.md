# Using-Pthyon-in-Blender-to-Copy-Animations-Movements
There is one rig completely animated and another unanimated. The first rig rotations and positions will be copied and translated to the second rig.


The animations where purchased at the unity asset store: https://assetstore.unity.com/packages/3d/animations/frank-slash-pack-5-2-katana-assassin-warrior-dual-2handed-spear--141184

The fbx files was exported by unity and loaded into blender. Every fbx file represent a different animation(walk, run, stand, etc.). This is known as "Armature". RobloxRig is a roblox rig with no animations. 

The goal was to copy the unity fbx animations to RobloxRig. This was done by the following steps.


1. First the scene is empty, upload an "Armature"(unity fbx animation) preferable an idle one and RobloxRig. 
2. Fix the rig to have the same tpose. This must be done manually, rotate the bones in the Armature to match the RobloxRig so that they have the same pose
3. This "Armature" will be used a reference when uploading new animations. Upload a new Armature(unity fbx animations) this will be known as "Armature.001". It will be the same model as "Armature". "Armature.001" is the animations we are copying.

4. Next copy the bones of "Armature" to "Armature.001". This is so that it has the same pose as "Armature" and thus the same pose as the RobloxRig

        def CopyBones(Armature1, Armature2):

            A_Bones = Armature1.pose.bones
            A2_Bones = Armature2.pose.bones

            #x = 2
            for x in ArrayCloudBonesOrig:

                if x:
                    boneSource = Armature1.pose.bones[x]
                    boneTarget = Armature2.pose.bones[x]

                    bpy.context.view_layer.update()
                    #print("Other Bone: " ,boneTarget)
                    #tail = bpy.data.objects["Armature2"].location + A2_Bones[1].tail
                    #head = bpy.data.objects["Armature2"].location + A2_Bones[1].head

                    #print(tail, head)

                    pb = boneTarget
                    mw = Armature2.convert_space(pose_bone=pb, 
                            matrix=pb.matrix, 
                            from_space='POSE', 
                            to_space='WORLD')

                    pb2 = boneSource
                    mw2 = Armature1.convert_space(pose_bone=pb2, 
                            matrix=pb2.matrix, 
                            from_space='POSE', 
                            to_space='WORLD')

                    loc, rot, scale = mw.decompose()
                    loc2, rot2, scale2 = mw2.decompose()
                    #print("BoneA2: " ,loc)
                    #print("BoneA: " ,loc2)

                    mw.translation = loc2

                    pb.matrix = Armature2.convert_space(pose_bone=pb, 
                            matrix=mw, 
                            from_space='WORLD', 
                            to_space='POSE') 

                    bpy.context.view_layer.update()
                    snap_rotation(pb2, pb)
                    
                    
5. Create new bones in "Armature.001" that is a copy of RobloxRig:

      def AddBones(Rig,ArrayName,ArrayNameOrig):

          bpy.ops.object.mode_set(mode='EDIT', toggle=False)

          for x in range(1, 16):

              if ArrayCloudBonesOrig[x]:
                  ebs = Rig.data.edit_bones

                  #Create new bone
                  eb = ebs.new(ArrayCloudBones[x])

                  eb.head = (0, 1, 1)
                  Rig.data.edit_bones[ArrayCloudBones[x]].parent = Rig.data.edit_bones[ArrayCloudBonesOrig[x]]
                  eb.head = Rig.data.edit_bones[ArrayCloudBonesOrig[x]].head
                  eb.tail = Rig.data.edit_bones[ArrayCloudBonesOrig[x]].head + Vector((5,5,5))

These bones will represent the normal position and rotation of "RobloxRig". As these bones change so will the "RobloxRig" bones with further code. These new bones also do not affect "Armature.001" animation and are just reference points . For this to work you need to know the bones in "Armature.001" and "RobloxRig"
          ArrayCloudBonesOrig[1] = "pelvis"
          ArrayCloudBonesOrig[2] = "thigh_r"
          ArrayCloudBonesOrig[3] = "calf_r"
          ArrayCloudBonesOrig[4] = "foot_r"

          ArrayCloudBonesOrig[5] = "thigh_l"
          ArrayCloudBonesOrig[6] = "calf_l"
          ArrayCloudBonesOrig[7] = "foot_l"

          ArrayCloudBonesOrig[8] = "spine_02"



          ArrayCloudBonesOrig[9] = "upperarm_r"
          ArrayCloudBonesOrig[10] = "lowerarm_r"
          ArrayCloudBonesOrig[11] = "hand_r"

          ArrayCloudBonesOrig[12] = "upperarm_l"
          ArrayCloudBonesOrig[13] = "lowerarm_l"
          ArrayCloudBonesOrig[14] = "hand_l"

          ArrayCloudBonesOrig[15] = "head"


          # 1  - LowerTorso
          ArrayCloudBones[1] = "LowerTorso"
          # 2  - RightUpperLeg
          ArrayCloudBones[2] = "RightUpperLeg"
          # 3  - RightLowerLeg
          ArrayCloudBones[3] = "RightLowerLeg"
          # 4  - RightFoot
          ArrayCloudBones[4] = "RightFoot"
          # 5  - LeftUpperLeg
          ArrayCloudBones[5] = "LeftUpperLeg"
          # 6  - LeftLowerLeg
          ArrayCloudBones[6] = "LeftLowerLeg"
          # 7  - LeftFoot
          ArrayCloudBones[7] = "LeftFoot"
          # 8  - UpperTorso
          ArrayCloudBones[8] = "UpperTorso"
          # 9  - RightUpperArm
          ArrayCloudBones[9] = "RightUpperArm"
          # 10 - RightLowerArm
          ArrayCloudBones[10] = "RightLowerArm"
          # 11 - RightHand
          ArrayCloudBones[11] = "RightHand"
          # 12 - LeftUpperArm
          ArrayCloudBones[12] = "LeftUpperArm"
          # 13 - LeftLowerArm
          ArrayCloudBones[13] = "LeftLowerArm"
          # 14 - LeftHand
          ArrayCloudBones[14] = "LeftHand"
          # 15 - Head
          ArrayCloudBones[15] = "Head"

Bones must be defined so you can attact them apropriately.

6. After adding the bones to "Armature.001", Make the new bones are properly aligned with the bones in "RobloxRig". This will ensure that the bones are is the right location and position. Rememeber, "Armature.001" and "RobloxRig" should be in the same pose. 

          def NormalizeBones(Rig,ArrayNewBones):


            bpy.ops.object.mode_set(mode='POSE')

            for x in range(1, 16):
                print(ArrayNewBones[x])
                if ArrayNewBones[x]:
                    #print(x,"R ", R_Bones[x], "A",Rig.pose.bones[ArrayNewBones[x]],ArrayNewBones[x])
                    snap_rotation(
                       R_Bones[x],
                       Rig.pose.bones[ArrayNewBones[x]]
                    )
                    bone = Rig.pose.bones[ArrayNewBones[x]]
                    bone.scale = Vector( (1, 1, 1) )
                    bone.keyframe_insert(data_path='rotation_quaternion',frame=0)
                    bone.keyframe_insert(data_path="location", frame=0)
                    bone.keyframe_insert(data_path="scale", frame=0)

7. Now we can copy the animations. As the frame changes so does the "Armature.001" and we need to translate it to the "RobloxRig". Snap rotation copies one bones rotation and paste it on another.

          def snap_rotation(source, target):
              Ms = source.id_data.convert_space(
                  pose_bone=source,
                  matrix=source.matrix,
                  from_space='POSE',
                  )
              Mt = target.id_data.convert_space(
                  pose_bone=target,
                  matrix=target.matrix,
                  from_space='POSE',
                  ) 

              _, q, _ = Ms.decompose()
              t, _, s = Mt.decompose()
              M = (
                  Matrix.Translation(t) @
                  q.to_matrix().to_4x4() @ 
                  Matrix.Diagonal(s.to_4d())
                  )
              target.matrix = target.id_data.convert_space(
                  pose_bone=target,
                  matrix=M,
                  to_space='POSE',
                  )    
                  
We do this for every bone.

            def SetToBone(Rig): 

                bpy.ops.object.mode_set(mode='POSE')    
                for x in range(1, 16):

                    y = 15 - x
                    if ArrayCloudBones[y]:
                        snap_rotation( 
                           Rig.pose.bones[ArrayCloudBones[y]],
                           R_Bones[y]
                        ) 
                       
And for every frame


            def SetKeyFrames(Rig, FrameStart,FrameEnd):  

                for f in range(FrameStart, FrameEnd):

                    bpy.context.view_layer.update()
                    scene.frame_current = f

                    bpy.context.view_layer.update()


                    for x in range(1, 16):
                        bpy.context.view_layer.update()
                        SetToBone(Rig)


                    for bone in R_Bones:
                        bone.scale = Vector( (1, 1, 1) )
                        bone.keyframe_insert(data_path='rotation_quaternion',frame=f)
                        bone.keyframe_insert(data_path="location", frame=f)
                        bone.keyframe_insert(data_path="scale", frame=f)
                        #bone.scale = Vector( (1, 1, 1) )
                        bpy.context.view_layer.update()
