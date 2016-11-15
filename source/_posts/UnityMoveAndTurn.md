---
title: Unity Move and Turn
tags: 'unity, C#'
date: 2016-10-24 17:55:55
---


在這裡記錄一下unity對一個rigidbody最簡單的move 跟 turn 

### Move 
move 就是移動，這裡用的是 MovePosition， 官網是這樣寫的 
		
		public void MovePosition(Vector3 position);

Vector3是一個3D 的點，
按照我的理解，就是直接給這個rigidbody一個新的位置，然後簡單粗暴地移動到新的位置，沒有考慮重力，摩擦摩擦等因素。
舉一個栗子： 
		
		float v = Input.GetAxis ("Vertical");
		Vector3 movement = transform.forward * v * Speed * Time.deltaTime;
        rigidbody.MovePosition(rigidbody.position + movement);

這樣，這個rigidbody就會不停向前走


### Turn
turn  就是旋轉， 比移動要複雜一點點。 這裡用的是 MoveRotation， 同樣，官網是這樣寫的 
		
		public void MoveRotation(Quaternion rot);

注意，這裡的參數系Quaternion, 中文是四元数（握草，什麼鬼）。
要搞到這個Quaternion，我們要用unity 的 Quaternion.Euler 方法 
		
		Quaternion Euler(float x, float y, float z);

##### Returns a rotation that rotates z degrees around the z axis, 
##### x degrees around the x axis, and y degrees around the y axis (in that order).
這個function是傳入x,y,z三個 float ，輸出 Quaternion , 有了這個Quaternion之後就可以用來旋轉
這個rigidbody 了，下面是例子：  
		
		
		float turn = Input.GetAxis ("Horizontal") * Speed * Time.deltaTime;

		Quaternion turnRotation = Quaternion.Euler (0f, turn, 0f);

        rigidbody.MoveRotation (rigidbody.rotation * turnRotation);

其實也可以直接這樣

		rigidbody.rotation = turnRotation;

因為rigidbody.rotation 本身就是一個 Quaternion。 


如果想一個rigidbody 總是看著另一個rigidbody 就可以用 Quaternion.LookRotation(Vector3 forward, Vector3 upwards = Vector3.up)
方法計算出一個Quaternion 

	public static Quaternion LookRotation(Vector3 forward, Vector3 upwards = Vector3.up);

或者更直接用 Transform.LookAt 
	
	public void LookAt(Transform target, Vector3 worldUp = Vector3.up);

	public Transform target;
    
    void Update() {
    	// Rotate the camera every frame so it keeps looking at the target 
        transform.LookAt(target);
    }


上面就是對於一個 rigidbody的移動和旋轉的方法。


### 這裡再稍微說一下 MovePosition 跟 Rigidbody.AddForce() 的區別
MovePosition 是提供一個新的位置，讓rigidbody 從舊的位置去到新的位置，可以實現瞬移，跟物理學沒有半點關係 （瞬移根本就違反物理學， ps:這裡是2016年的物理學）
addforce 是加一個力給這個rigidbody 讓它移動，像推它，踢它甚至爆炸的力，如果摩擦力為0，根據物理學，物體會一直運動， addforce 不能實現瞬移。 


