# SwiftChess

[![Version](https://img.shields.io/cocoapods/v/SwiftChess.svg?style=flat)](http://cocoapods.org/pods/SwiftChess)
[![License](https://img.shields.io/cocoapods/l/SwiftChess.svg?style=flat)](http://cocoapods.org/pods/SwiftChess)
[![Platform](https://img.shields.io/cocoapods/p/SwiftChess.svg?style=flat)](http://cocoapods.org/pods/SwiftChess)
[![CI Status](https://api.travis-ci.org/SteveBarnegren/SwiftChess.svg?branch=master)](https://travis-ci.org/SteveBarnegren/SwiftChess)
[![Twitter](https://img.shields.io/badge/contact-@stevebarnegren-blue.svg?style=flat)](https://twitter.com/stevebarnegren)

SwiftChess is a chess engine written in Swift.

![swiftchess](https://cloud.githubusercontent.com/assets/6288713/24018928/d90f9182-0a8d-11e7-808c-a96bcb998462.gif)

## Features

- Move validation
- AI with three difficulty levels
- Callbacks for check, checkmate and stalemate
- Supports castling
- Supports En Passent
- Supports pawn promotion
- Asyncronous AI move calculation


SwiftChess doesn't provide any UI, just all of the logic required to create a chess game. The example project contains a complete UIKit UI with touch handling that you start from if you like.

## Example

The example application contains a complete implementation of **SwiftChess**.

Run `Example/Example.xcodeproj`

## Basic Use

##### Start a game

```
// Make a human player
let whitePlayer = Human(color: .white)

// ... or an AI Player
let blackPlayer = AIPlayer(color: .black, configuration: AIConfiguration(difficulty: .hard))

// Create a game       
let game = Game(firstPlayer: whitePlayer, secondPlayer: blackPlayer)
```

##### Make a move

```
if let player = game.currentPlayer as? Human {

	let currentLocation = BoardLocation(x: 4, y: 1)
	let newLocation = BoardLocation(x: 4, y: 2)

	try! player.movePiece(from: currentLocation,
                        to: newLocation)
}
```

##### Tell the AI to make a move

```
if let player =  game.currentPlayer as? AIPlayer {
	player.makeMoveAsync()
}
```
##### Then just wait for the callbacks!

```
extension GameViewController: GameDelegate {

	func gameDidMovePiece(game: Game, piece: Piece, toLocation: BoardLocation) {
        // Move piece on board
    }
    
    func gameDidRemovePiece(game: Game, piece: Piece, location: BoardLocation) {
        // Remove piece from board 
    }
    
    func gameDidTransformPiece(game: Game, piece: Piece, location: BoardLocation) {
    	// A pawn was promoted!
    }
    
    func gameWonByPlayer(game: Game, player: Player) {
    	ShowAlert("Checkmate!")
    }
    
    func gameEndedInStaleMate(game: Game) {
     	ShowAlert("Stalemate!")
    }
    
    func gameDidChangeCurrentPlayer(game: Game) {
    	// Make another move        
    }
}
```

## Persistence

The entire state of a **SwiftChess** game can be converted to and initialised from a `Dictionary`.

Get a snapshot of the current state:

```
let dictionary: [String: Any] = game.dictionaryRepresentation
```

Initialise a game with a previous snapshot:

```
let game = Game(dictionary: dictionary)
```

The returned dictionary stores all the information required to create a 'save game' feature. The player colors, AI difficulty, piece positions etc.

You can serialize this to JSON, save it to disk, send it over the network etc.


## Other stuff

##### Make a castling move

```
if game.board.canColorCastle(color: .white, side: .kingSide) {
	player.performCastleMove(side: .kingSide)
}
```

##### Support pawn promotion

```
func promotedTypeForPawn(location: BoardLocation, 
                         player: Human, 
                         possiblePromotions: [Piece.PieceType], 
                         callback: @escaping (Piece.PieceType) -> Void) {

	// Show UI for the user to select one of the possible promotion types
	// then call the handler
	
	// ...or some games just promote to a queen
	callback(.queen)
}
```
