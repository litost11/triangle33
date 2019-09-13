# triangle33
#include <iostream>
#include <string>
using namespace std;


//MAXSIZE and MINSIZE are the maximum and minum sizes for the board
const int MAXSIZE = 25; 
const int MINSIZE = 8;
//MINCONNECTNUM is the and minum lengths of the line need to win the game
const int MINCONNECTNUM = 4;


bool InitializeBoard(int** connectNBoard, int numRowsInBoard);

bool MakeMove(int** connectNBoard, int numRowsInBoard, int playerID, int columnChosen);

bool DisplayBoard(int** connectNBoard, int numRowsInBoard);

bool CheckWinner(int** connectNBoard, int numRowsInBoard, int numConnect, int columnChosen, int playerID);


int main()
{   
	//Define the local variables for the main program
	int playerID = 1; 
	int numRows = 0;
	int numToConnect = 0;
	int **myConnectNBoard = NULL;
	int chosenColumn;

	
	//loop coounter
	int i = 0; 
	int j = 0; 


	//Prompt for and read the size of the board (numRows)
	//using infinite loop until get the acceptable input to break
    while (1)
	{
		cout << "Enter the number of squares along each edge of board" << endl;
		cin >> numRows;

		if (cin.fail())
		{
			cout << "ERROR: The value provided was not a number" << endl;
            cin.clear();
            cin.ignore(32768,'\n');
		}

		else if (numRows > MAXSIZE || numRows < MINSIZE)
		{
			cout << "ERROR: Board size too large (>" << MAXSIZE << ")" << endl;
			cout << "ERROR: Or board size too small (<" << MINSIZE << ")" << endl;
			
		}

		else { break;}
        
	}

	
	//Prompt for and read the number of game pieces in a line 
	//that are needed to win the game (numToConnect)  
	//using infinite loop until get the acceptable input to break
	while (1)
	{
		cout << "Enter the number of pieces that must form a line to win" << endl;
		cin >> numToConnect;

		if (cin.fail())
		{
			cout << "ERROR: The value provided was not a number" << endl;
            cin.clear();
            cin.ignore(32768,'\n');
		}

		else if (numToConnect > numRows - MINCONNECTNUM || numToConnect < MINCONNECTNUM)
		{
			cout << "ERROR: Number to connect is too large (>" << numRows - MINCONNECTNUM << ")" << endl;
			cout << "ERROR: Or Number to connect is too small (<" << MINCONNECTNUM << ")" << endl;
			continue;
		}

		else { break;}
        
	}

		
    //Dynamically allocate an integer array
    //to hold a game board of the specified size
	myConnectNBoard = new int *[numRows];

	for (i = 0; i < numRows; i++)
	{
		myConnectNBoard[i] = new int[numRows];
	}


    //Initialize the game board using the InitializeBoard function
    //Check to see that the board initialized correctly (InitializeBoard returned true)
    //If the board does not initialize correctly print the error message
    //then terminate the main program
    if (!InitializeBoard(myConnectNBoard, numRows))
	{
		cout << "ERROR: Could not initialize the game board" << endl;
		exit(1);
	}


	while(1)
	{

		// Check to see that the board displayed correctly (DisplayBoard returned true)
		// If the board does not display correctly print the error message 
		if (!DisplayBoard(myConnectNBoard, numRows))
		{
			cout << "ERROR: Could not display the game board" << endl;
			continue;
		}

        //Check which player
		if (playerID == 1)
		{
			cout << "Red Moves:" << endl;
		}
		else if (playerID == 2)
		{
			cout << "Black moves:" << endl;
		}


        //Prompt for the number of the column on which the player wishes to place his piece(chosenColumn)
        //If the entered value is not a number then print the error message
        //If the chosen column is not on the board print the error message 
        //If it all squares in the chosen column are occupied print the error message
		for (i = 0; i < 3; i++)
		{
			cout << "Enter the column number where you want to put your piece" << endl;
			cin >> chosenColumn;

			if (cin.fail())
			{
				cout << "ERROR: The value provided was not a number" << endl;
				continue;
			}


			else if (chosenColumn < 0 || chosenColumn >= numRows)
			{
				cout << "ERROR: Column " << chosenColumn << " is not on the board: try again" << endl;
				cout << "ERROR: Column number should be >= 0 and < " << numRows << endl;
				continue;
			}

			if (myConnectNBoard[0][chosenColumn] != 0)
			{
				cout << "ERROR: Column " << chosenColumn << " is already completely full try again" << endl;
				continue;
			}

			if (!MakeMove(myConnectNBoard, numRows, playerID, chosenColumn))
			{
				continue;
			}
			else
			{
				break;
			}
		}


        //If three tries at making a legal move have all failed the player forfeits their turn
        //When a turn is forfeited then the messages below are printed
		if (i == 3)
		{
			cout << "TOO MANY ILLEGAL MOVES" << endl;
			if (playerID == 1)
			{
				cout << "Red has forfeited a turn" << endl;
			}
			else if (playerID == 2)
			{
				cout << "Black has forfeited a turn" << endl;
			}

		}
		else
		{
			if (CheckWinner(myConnectNBoard, numRows, numToConnect, chosenColumn, playerID))
			{
				if (playerID == 1)
				{
					cout << "Red player has won" << endl;
				}
				else if (playerID == 2 )
				{
					cout << "Black player has won" << endl;
				}

				exit(2);
			}
			
		}


        //switch players
		if (playerID == 1)
		{
			playerID = 2;
		}
		else if (playerID == 2)
		{
			playerID = 1;
		}
		
	}
	return 0;
}






bool InitializeBoard(int** connectNBoard, int numRowsInBoard)
{

	// Initialize the connectNBoard to be have an 0 in each element
    //If the poniter is NULL print error massage
    //If the number of rows in board is illegal print error massage
    //loop counter;
    // if the move is illegal return false 
	int i = 0;
	int j = 0;

	if (connectNBoard == NULL )
	{
		cout << "ERROR: cannot initialize the board because the pointer to the board is NULL" << endl;
		return false;
	}
	

	for (i = 0; i < numRowsInBoard; i++)
	{
		if (connectNBoard[i] == NULL)
		{
			cout << "ERROR: cannot initialize the board because the pointer to the board is NULL" << endl;
			return false;
		}
	}

	if (numRowsInBoard > MAXSIZE)
	{
		cout << "ERROR: illegal number of rows in board" << endl;
		return false;
	}
	else if (numRowsInBoard < MINSIZE)
	{
		cout << "ERROR: illegal number of rows in board" << endl;
		return false;
	}

	
	for (i = 0; i < numRowsInBoard; i++)
	{
		for (j = 0; j < numRowsInBoard; j++)
		{
			connectNBoard[i][j] = 0;
		}
	}
	return true;
}






bool MakeMove(int** connectNBoard, int numRowsInBoard, int playerID, int columnChosen)
{
    //print ERROR the board because the pointer to the board is NULL
	int i = 0; //loop counter

	if (connectNBoard == NULL)
	{
		cout << "ERROR: cannot print the board because the pointer to the board is NULL" << endl;
		return false;
	}
	
    //print Error when ith row of connectBoard is NULL
	for (i = 0; i < numRowsInBoard; i++)
	{
		if (connectNBoard[i] == NULL)
		{
			cout << "ERROR: cannot initialize the board because the pointer to the board is NULL" << endl;
			return false;
		}
	}

	// if the move is illegal return false 
	if ((columnChosen < 0 || columnChosen > numRowsInBoard) || 0 != connectNBoard[0][columnChosen])
	{
		cout << "Illegal move" << endl;
		return false;
	}

	// check the number of element to decide player 1 or 2 move 
	for (i = numRowsInBoard - 1; i >= 0; i--)
	{
		if (connectNBoard[i][columnChosen] == 0)
		{
			if (playerID == 1)
			{
				connectNBoard[i][columnChosen] = 1;
				cout << "Red has moved" << endl;
				break;
			}
			else
			{
				connectNBoard[i][columnChosen] = 2;
				cout << "Black has moved" << endl;
				break;

			}
		}
	}
	return true;
}






bool DisplayBoard(int** connectNBoard, int numRowsInBoard)
{
// loop counter 
	int i = 0;
	int j = 0;

	// if the whold connectBoard is Null then print error
	if (connectNBoard == NULL)
	{
		cout << "ERROR: cannot print the board because the pointer to the board is NULL" << endl;
		return false;
	}
	

	// print Error when ith row of connectBoard is NULL
	for (i = 0; i < numRowsInBoard; i++)
	{
		if (connectNBoard[i] == NULL)
		{
			cout << "ERROR: cannot initialize the board because the pointer to the board is NULL" << endl;
			return false;
		}
	}

	// If the move is illegal then return false 
	if (numRowsInBoard > MAXSIZE)
	{
		cout << "ERROR: illegal number of rows in board" << endl;
		return false;
	}
	else if (numRowsInBoard < MINSIZE)
	{
		cout << "ERROR: illegal number of rows in board" << endl;
		return false;
	}

	// blank line
	cout << '\n' << endl;
	cout << " ";
	
	for (i = 0; i < numRowsInBoard; i++)
	{
		cout << "  " << i;
	}
	cout << endl;

	// print Error when ith row of connectBoard is NULL
	// Replace 0,1,2 to o,R,B
	for (i = 0; i < numRowsInBoard; i++)
	{
		for (j = 0; j < numRowsInBoard + 1; j++)
		{
			if (connectNBoard[i] == NULL)
			{
				cout << "ERROR: cannot print the board because the pointer to the board is NULL" << endl;
				return false;
			}

			if (j == 0)
			{
				cout << i;
			}
			else
			{	
				if (connectNBoard[i][j - 1] == 0)
				{
					cout << "  " << 'o';
				}
				else if (connectNBoard[i][j - 1] == 1)
				{
					cout << "  " << 'R';
				}
				else if(connectNBoard[i][j - 1] == 2)
				{
					cout << "  " << 'B';
				}
			}
			
		}
		cout << endl;
	}
	return true;
}





bool CheckWinner(int** connectNBoard, int numRowsInBoard, int numConnect, int columnChosen, int playerID)
{
	//loop counter
	int i = 0;
	int j = 0;
	int rowNum = 0;
	int nCount = 0;

	// If invalid column chosen then print error
	if (columnChosen < 0 || columnChosen > numRowsInBoard)
	{
		cout << "ERROR:  invalid column chosen, cannot check for winner" << endl;
		return false;
	}

	// If the connectNBoard is Null then print error
	if (connectNBoard == NULL)
	{
		cout << "ERROR: cannot print the board because the pointer to the board is NULL" << endl;
		return false;
	}
	

	// print Error when ith row of connectBoard is NULL
	for (i = 0; i < numRowsInBoard; i++)
	{
		if ( connectNBoard[i]== NULL)
		{
			cout << "ERROR: cannot initialize the board because the pointer to the board is NULL" << endl;
			return false;
		}
	}

	// print the number of element of nonzero row  
	for (i = 0; i < numRowsInBoard; i++)
	{
		if (connectNBoard[i][columnChosen] != 0)
		{
			rowNum = i;
			break;
		}
	}

	
	//judging line
	for (i = columnChosen - 1; i >= 0; i--)
	{
		if (playerID != connectNBoard[rowNum][i])
		{
			break;
		}
		nCount++;
	}
	for (i = columnChosen + 1; i < numRowsInBoard; i++)
	{
		if (playerID != connectNBoard[rowNum][i])
		{
			break;
		}
		nCount++;
	}
	nCount++;


	if (nCount == numConnect)
	{
		return true;
	}
	//judging colunm
	nCount = 0;

	for (i = rowNum; i < numRowsInBoard; i++)
	{
		if (playerID != connectNBoard[i][columnChosen])
		{
			break;
		}
		nCount++;
	}
	if (nCount == numConnect)
	{
		return true;
	}
	


	//jugding left diagonal
	nCount = 0;
	for (i = rowNum - 1,j = columnChosen - 1; i >= 0 && j >= 0; i--,j--)
	{
		if (playerID != connectNBoard[i][j])
		{
				break;
		}
		nCount++;
	}


	for (i = rowNum + 1, j = columnChosen + 1; i < numRowsInBoard && j < numRowsInBoard; i++,j++)
	{
		if (playerID != connectNBoard[i][j])
		{
			break;
		}
		nCount++;
	}
	nCount++;
	if (nCount == numConnect)
	{
		return true;
	}
	


	//jugding right diagonal
	nCount = 0;
	for (i = rowNum - 1, j = columnChosen + 1; i >= 0 && j < numRowsInBoard; i--, j++)
	{
		if (playerID != connectNBoard[i][j])
		{
			break;
		}
		nCount++;
	}

	
	for (i = rowNum + 1, j = columnChosen - 1; i < numRowsInBoard && j >= 0; i++, j--)
	{
		if (playerID != connectNBoard[i][j])
		{
			break;
		}
			nCount++;
	}
    nCount++;
	if (nCount == numConnect)
	{
		return true;
	}


	return false;
}
