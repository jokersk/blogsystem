---
title: phaser
date: 2016-11-04 10:43:43
tags:
---

遊戲框架

		var game;


		var gameOptions = {
			gameWidth: 1000, 
			gameHeight: 800
			
		}

		window.onload = function() {	
			 game = new Phaser.Game(gameOptions.gameWidth, gameOptions.gameHeight);
		     game.state.add("TheGame", TheGame);
		     game.state.start("TheGame");
		}

		var TheGame = function(){};

		TheGame.prototype = {
		     
		     
		     preload: function(){
		          // game.load.spritesheet("tiles", "titlePath", tilesSize, tilesSize);
		          // game.load.image("player", "playerPath"); 
		     },
		     
		     
		  	create: function(){
		        game.scale.scaleMode = Phaser.ScaleManager.SHOW_ALL;
				game.scale.pageAlignHorizontally = true;
				game.scale.pageAlignVertically = true; 
		          
		  	},

		  	update:function(){

		  	}
     
		}

