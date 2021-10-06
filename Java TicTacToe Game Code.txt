import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.io.BufferedWriter;
import java.io.FileWriter;
import java.io.IOException;

import static com.sun.deploy.uitoolkit.ToolkitStore.dispose;

public class TicTacToe extends JPanel {
    JButton buttons[] = new JButton[9];
    int alternate = 0;//if this number is a even, then put a X. If it's odd, then put an O

    int winPoints,games,numberOfGames,xWin,yWin;

    public TicTacToe()
    {
        setLayout(new GridLayout(3,3));
        initializeButtons();
        numberOfGames = 0;
        games = 0;
        xWin=0;
        yWin=0;
    }

    public void initializeButtons()
    {
        for(int i = 0; i <= 8; i++)
        {
            buttons[i] = new JButton();
            buttons[i].setText("");
            buttons[i].addActionListener(new ButtonListener());

            add(buttons[i]); //adds this button to JPanel (note: no need for JPanel.add(...)
            //because this whole class is a JPanel already

        }
    }
    public void resetButtons()
    {
        for(int i = 0; i <= 8; i++)
        {
            buttons[i].setText("");
            buttons[i].setEnabled(true);
        }
    }

    // when a button is clicked, it generates an ActionEvent. Thus, each button needs an ActionListener. When it is clicked, it goes to this listener class that I have created and goes to the actionPerformed method. There (and in this class), we decide what we want to do.
    private class ButtonListener implements ActionListener {

        public void actionPerformed(ActionEvent e)
        {


            JButton buttonClicked = (JButton)e.getSource(); //get the particular button that was clicked
            buttonClicked.setFont(new Font("Century Schoolbook L", 1, 30));
            if(buttonClicked == e.getSource())
            {
                buttonClicked.setEnabled(false);

            }

            if(alternate%2 == 0) {
                buttonClicked.setText("X");
                if(checkForWin() == true)
                {

                    FileWriter fileWriter = null;
                    BufferedWriter bufferedWriter = null;

                    try {
                        fileWriter = new FileWriter("gameReportFile.txt", true);
                        bufferedWriter = new BufferedWriter(fileWriter);
                    } catch (IOException ex) {
                        ex.printStackTrace();
                    }

                    try {
                        while (checkForWin() == true) {

                            if (numberOfGames >= 0) {
                                games++;
                                bufferedWriter.write("Game " + games);
                                bufferedWriter.newLine();
                            }
                            if (winPoints >= 0) {
                                xWin++;
                                bufferedWriter.write("X player win " + xWin + " time/s");
                            }
                            break;
                        }

                        bufferedWriter.newLine();
                        bufferedWriter.newLine();
                        bufferedWriter.close();
                    } catch (IOException ex) {
                        ex.printStackTrace();
                    }

                    int dialogButton = JOptionPane.showConfirmDialog(null,"X player win, do you want to play again?.");
                    if (dialogButton == JOptionPane.YES_OPTION) {
                        resetButtons();

                    }
                    else if (dialogButton == JOptionPane.NO_OPTION || dialogButton == JOptionPane.CANCEL_OPTION) {
                        setVisible(false);
                        try {
                            dispose();
                            System.exit(0);
                        } catch (Exception e1) {
                            e1.printStackTrace();
                        }
                    }
                }
            }
            else if(alternate %2 == 1) {
                buttonClicked.setText("O");
                if(checkForWin() == true)
                {

                    FileWriter fileWriter = null;
                    BufferedWriter bufferedWriter = null;

                    try {
                        fileWriter = new FileWriter("gameReportFile.txt", true);
                        bufferedWriter = new BufferedWriter(fileWriter);
                    } catch (IOException ex) {
                        ex.printStackTrace();
                    }

                    try {


                        while (checkForWin() == true) {
                            if (numberOfGames >= 0) {
                                games++;
                                bufferedWriter.write("Game " + games);
                                bufferedWriter.newLine();
                            }
                            if (winPoints >= 0) {
                                yWin++;
                                bufferedWriter.write("O player win " + yWin + " time/s");
                            }
                            break;
                        }

                        bufferedWriter.newLine();
                        bufferedWriter.newLine();
                        bufferedWriter.close();
                    } catch (IOException ex) {
                        ex.printStackTrace();
                    }

                    int dialogButton = JOptionPane.showConfirmDialog(null,"O player win, do you want to play again?.");
                    if (dialogButton == JOptionPane.YES_OPTION) {
                        resetButtons();
                    }
                    else if (dialogButton == JOptionPane.NO_OPTION || dialogButton == JOptionPane.CANCEL_OPTION) {
                        setVisible(false);
                        try {
                            dispose();
                            System.exit(0);
                        } catch (Exception e1) {
                            e1.printStackTrace();
                        }
                    }
                }
            }

            else if (alternate <= 9 && checkForWin() != true) {
                int dialogButton = JOptionPane.showConfirmDialog(null, "No one win, do you want to play again?.");
                if (dialogButton == JOptionPane.YES_OPTION) {
                    resetButtons();
                } else if (dialogButton == JOptionPane.NO_OPTION || dialogButton == JOptionPane.CANCEL_OPTION) {
                    setVisible(false);
                    try {
                        dispose();
                        System.exit(0);
                    } catch (Exception e1) {
                        e1.printStackTrace();
                    }
                }
            }

            alternate++;

        }

        public boolean checkForWin() {
            /**   Reference: the button array is arranged like this as the board
             *      0 | 1 | 2
             *      3 | 4 | 5
             *      6 | 7 | 8
             */


            //horizontal win check
            if( checkAdjacent(0,1) && checkAdjacent(1,2) ) { //no need to put " == true" because the default check is for true
                return true;
            }
            else if( checkAdjacent(3,4) && checkAdjacent(4,5) ) {
                return true;
            }
            else if ( checkAdjacent(6,7) && checkAdjacent(7,8)) {
                return true;
            }

            //vertical win check
            else if ( checkAdjacent(0,3) && checkAdjacent(3,6)) {
                return true;
            }
            else if ( checkAdjacent(1,4) && checkAdjacent(4,7)) {
                return true;
            }
            else if ( checkAdjacent(2,5) && checkAdjacent(5,8)) {
                return true;
            }

            //diagonal win check
            else if ( checkAdjacent(0,4) && checkAdjacent(4,8)) {
                return true;
            }
            else if ( checkAdjacent(2,4) && checkAdjacent(4,6)) {
                return true;
            }
            else {
                return false;
            }


        }

        public boolean checkAdjacent(int a, int b)
        {
            if ( buttons[a].getText().equals(buttons[b].getText()) && !buttons[a].getText().equals("") )
                return true;
            else
                return false;
        }

    }

    public static void main(String[] args)
    {
        JFrame window = new JFrame("Tic Tac Toe Game");
        window.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        window.getContentPane().add(new TicTacToe());
        window.setBounds(550,300,300,300);
        window.setVisible(true);
    }
}
