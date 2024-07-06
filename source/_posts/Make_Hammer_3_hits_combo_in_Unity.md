---
title: Make Hammer 3 hits combo in Unity
tags: [Unity, maya]
category: Unity
index_img: /img/hammer.jfif
banner_img: /img/MakeHammer.gif
archive: false
---

## Unity Game Play View

  <video width="850" controls>
  <source src="/codeArt-TechBlog/video/Hammer3Hit.mp4" type="video/mp4">
  </video>

## Design and Thoughts Hammer 3 hits combo

### key pose and spacing

1. 重量感 key pose and spacing
2. 誇張 key pose
3. 重量緩衝預備
4. 重量與動作拉扯感

### 遊戲攻擊節奏 spacing

1. 強調重型武器的重量感，預備與緩衝動作間距小且速度慢，攻擊動作間距大且速度快，如下圖以 Attack 02 為例
   - Attack02
     <img src="/codeArt-TechBlog/img/unity/Attack02.png" width="800" style="display: block; margin-left: 0;">

2. 重型武器，hits ( atk01-40 frames、atk02-33 frames、atk03-56 frames ) 如下參考 3 hit combo
   - 當攻擊間距較大且速度快與預備緩衝間距小且速度慢，會形成反差的對比，玩家在體感上也較可以明顯感受武器的重量
    <video width="800" controls>
    <source src="/codeArt-TechBlog/video/3_hit_combo_motion_trail.mp4" type="video/mp4">
    </video>

## Import process and settings & build game

### maya export fbx option item

#### option
<img src="/codeArt-TechBlog/img/unity/mayaExportFbx.png" width="400" style="display: block; margin-left: 0;">

1. Axis Conversion (座標軸轉換)：這個選項允許你設置座標軸之間的轉換。你可以根據目標應用程序的座標系統來調整轉換方式，以確保FBX文件在其他軟件中的位置和方向正確。
2. Animation (動畫)：
    - Bake Animation (烘焙動畫)：烘焙動畫將動畫數據轉換為關鍵幀，並將其嵌入到FBX文件中。這有助於確保在其他軟件中播放動畫時的一致性和準確性。
    - Animation Layers (動畫層)：選擇是否導出使用了動畫層的動畫數據。
    - Deformed Models (變形模型)：選擇是否導出變形模型，包括綁定的皮膚、骨骼和權重等信息。
    - Constraints (約束)：選擇是否導出場景中的約束條件，例如點約束、方向約束等。
3. Geometry (幾何體)：
    - Smoothing Groups (平滑群組)：選擇是否導出平滑群組信息，以保留模型的平滑外觀。
    - Tangents and Binormals (切線和副法線)：選擇是否導出切線和副法線信息，用於光照和渲染效果。
    - Skins (蒙皮)：選擇是否導出模型的蒙皮信息，用於骨骼變形。
    - Blend Shapes (形狀融合)：選擇是否導出形狀融合信息，用於模型的表情和變形。
4. Cameras (相機)：選擇是否導出場景中的相機信息，包括位置、方向、焦距等。
HFOV (Unreal)
VFOF (Unity)
5. Lights (燈光)：選擇是否導出場景中的燈光信息，包括位置、類型、光強度等。
6. Embed Media (嵌入媒體)：選擇是否將媒體文件嵌入到FBX文件中。這包括導出使用的紋理貼圖、材質、音頻文件等。如果你選擇嵌入媒體，那麼FBX文件將成為一個獨立的文件，不再需要依賴外部媒體文件。
7. Embed Textures (嵌入紋理)：選擇是否將紋理貼圖嵌入到FBX文件中。如果你選擇嵌入紋理，那麼紋理文件將成為FBX文件的一部分，不再需要單獨的紋理文件。
8. Up Axis (上軸)：設置FBX文件中的上軸方向。根據不同的應用程序和預設設置，你可以選擇將上軸設置為X、Y或Z軸。
9. Collada (DAE) Options (Collada (DAE) 選項)：如果你要導出Collada格式（.dae），你可以在這裡設置相關選項，例如導出點約束、動畫等。
10. Constraints (約束)：選擇是否導出場景中的約束信息，例如鏈接約束、點約束等。
11. Include Cameras (包括相機)：選擇是否導出場景中的相機。
12. Include Lights (包括燈光)：選擇是否導出場景中的燈光。

### Setting up the animation and blending attributes：

#### animation assets
<img src="/codeArt-TechBlog/img/unity/animation_assets.png.png" width="400" style="display: block; margin-left: 0;">

#### attributes
<img src="/codeArt-TechBlog/img/unity/Setting_up_the_animation_and_blending.png" width="800" style="display: block; margin-left: 0;">

### Setting up the character prefab and rig attributes：
<img src="/codeArt-TechBlog/img/unity/mayaExportFbx.png" width="400" style="display: block; margin-left: 0;">

### create player_camera

#### CameraFollow.cs
```cs
using UnityEngine;

public class CameraFollow : MonoBehaviour
{
  public Transform target; // 角色的 Transform
  public Vector3 offset;   // 相機和角色之間的偏移量
  public float smoothSpeed = 0.125f; // 平滑跟隨的速度

  void LateUpdate()
  {
      // 計算相機應該的位置
      Vector3 desiredPosition = target.position + offset;
      // 平滑移動相機到目標位置
      Vector3 smoothedPosition = Vector3.Lerp(transform.position, desiredPosition, smoothSpeed);
      transform.position = smoothedPosition;

      // 確保相機朝向角色
      transform.LookAt(target);
  }
}
```

### create scene & props
#### assets：
<img src="/codeArt-TechBlog/img/unity/env_assets.png" width="400" style="display: block; margin-left: 0;">

#### processing：
<video width="800" controls><source src="/codeArt-TechBlog/video/unity/create_scene.mp4" type="video/mp4"></video>

### add sounds to gameplay
  
  #### sounds assets
   <img src="/codeArt-TechBlog/img/unity/audio_assets.png" width="400" style="display: block; margin-left: 0;">

  #### setup in animation then add events to frmes bar
   <img src="/codeArt-TechBlog/img/unity/add_event_setup.png" width="400" style="display: block; margin-left: 0;">

### setup animator controller

#### controller assets
<img src="/codeArt-TechBlog/img/unity/setup_animator_controller.png" width="800" style="display: block; margin-left: 0;">

### animator controller

<video width="800" controls>
<source src="/codeArt-TechBlog/video/unity/animator_controller.mp4" type="video/mp4">
</video>

### sciprts

#### scripts assets
<img src="/codeArt-TechBlog/img/unity/scripts.png" width="400" style="display: block; margin-left: 0;">

#### Attack.cs
```cs
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Audio;

public class Attack : MonoBehaviour
{
    private Animator animator;
    private bool isAttacking = false;
    private AudioSource audioSource;
    public AudioClip attackSound01;  // Attack01 的音效檔
    public AudioClip attackSound02;  // Attack02 的音效檔
    public AudioClip attackSound03;  // Attack03 的音效檔


    // Start is called before the first frame update
    void Start()
    {
        animator = GetComponent<Animator>();
        audioSource = GetComponent<AudioSource>();

        if (audioSource == null)
        {
            Debug.LogError("AudioSource component is missing!");
        }
    }

    // Update is called once per frame
    void Update()
    {
        if (Input.GetKeyDown(KeyCode.Mouse0))
        {
            HandleAttack();
        }

        // 檢查當前動畫狀態，如果不是攻擊動畫，則重置攻擊狀態
        AnimatorStateInfo currentState = animator.GetCurrentAnimatorStateInfo(0);
        if (isAttacking && !currentState.IsName("ANI_Player_Warhammer_Attack01") && 
                          !currentState.IsName("ANI_Player_Warhammer_Attack02") && 
                          !currentState.IsName("ANI_Player_Warhammer_Attack03"))
        {
            isAttacking = false;
        }
    }

    void HandleAttack()
    {
        var currentState = animator.GetCurrentAnimatorStateInfo(0);
        Debug.Log("Current State: " + currentState.fullPathHash);

        if (currentState.IsName("ANI_Player_Warhammer_Attack02"))
        {
            // 如果當前的動畫狀態是 Attack02，立即觸發 Attack03
            Debug.Log("Triggering Attack03");
            animator.SetTrigger("Attack03Trigger");
        }
        else if (currentState.IsName("ANI_Player_Warhammer_Attack01"))
        {
            // 如果當前的動畫狀態是 Attack01，立即觸發 Attack02
            Debug.Log("Triggering Attack02");
            animator.SetTrigger("Attack02Trigger");
        }
        else
        {
            // 否則，觸發 Attack01
            Debug.Log("Triggering Attack01");
            isAttacking = true;
            animator.SetTrigger("Attack01Trigger");
        }
        
    }
    public void PlayAttackSound(int attackNumber)
    {
        Debug.Log("Attempting to play sound");
        AudioClip clipToPlay = null;
        switch (attackNumber)
        {
            case 1:
                clipToPlay = attackSound01;
                break;
            case 2:
                clipToPlay = attackSound02;
                break;
            case 3:
                clipToPlay = attackSound03;
                break;
            default:
                Debug.LogError("Invalid attack number!");
                return;
        }

        if (clipToPlay != null && audioSource != null)
        {
            Debug.Log("AudioSource and AudioClip are set correctly");
            audioSource.PlayOneShot(clipToPlay);
            Debug.Log("Playing Attack Sound " + attackNumber);
        }
        else
        {
            if (clipToPlay == null)
            {
                Debug.LogError("Attack sound is not set!");
            }
            if (audioSource == null)
            {
                Debug.LogError("AudioSource component is missing!");
            }
        }
    }
}
```