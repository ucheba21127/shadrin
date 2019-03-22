import java.awt.*;
import java.awt.event.*;
import java.util.Random;
import java.util.Vector;

import javax.swing.*;
import javax.swing.border.*;

public class XO extends JFrame implements ActionListener {
    /////////////////////////////////////////////////////////////////////////
////////-------------Fields-------------------------------------------///
    private static final long serialVersionUID = 1L;
    private JPanel jContentPane = null;
    private JPanel jPanel = null;
    private JPanel jPanel1 = null;
    private JButton New = null;
    private JButton Ex = null;
    private JButton[] pole = null;
    private JLabel StateGame = null;
    private int m[]= new int [9];
    private Random rand = new Random();

    private int ButtonsInGame, vin;
    final int 	NULL = 0,
            X = 1,
            O = (-1);
    /////////////////////////////////////////////////////////////////////////
///////------------------Main-----------------------------------------///
    public static void main(String[] args) {
        SwingUtilities.invokeLater(new Runnable() {
            public void run() {
                XO thisClass = new XO();
                thisClass.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
                thisClass.setVisible(true);
            }
        });
    }
/////////////////////////////////////////////////////////////////////////
///---------------Interface builders----------------------------------///
    /**
     * Constructor
     */
    public XO() {
        super();
        initialize();
    }

    /**
     * Initialize function
     */
    private void initialize() {
        this.setSize(240, 268);
        this.setContentPane(getJContentPane());
        this.setTitle("JFrame");
    }
    /**
     * Content builder
     */
    private JPanel getJContentPane() {
        if (jContentPane == null) {
            StateGame = new JLabel();
            StateGame.setText(vin_getter(3));
            StateGame.setHorizontalAlignment(SwingConstants.CENTER);
            StateGame.setBackground(Color.black);
            StateGame.setDisplayedMnemonic(KeyEvent.VK_UNDEFINED);
            jContentPane = new JPanel();
            jContentPane.setLayout(new BorderLayout());
            jContentPane.setBackground(new Color(102, 102, 255));
            jContentPane.add(getJPanel(), BorderLayout.NORTH);
            jContentPane.add(getJPanel1(), BorderLayout.CENTER);
            jContentPane.add(StateGame, BorderLayout.SOUTH);
        }
        return jContentPane;
    }
    /**
     * Panel for buttons
     */
    private JPanel getJPanel() {
        if (jPanel == null) {
            jPanel = new JPanel();
            jPanel.setLayout(new FlowLayout());
            jPanel.setBorder(BorderFactory.createBevelBorder(BevelBorder.LOWERED));
            jPanel.setBackground(new Color(102, 102, 102));
            jPanel.add(getNew(), null);
            jPanel.add(getEx(), null);
        }
        return jPanel;
    }
    /**
     * Button "New game"
     */
    private JButton getNew() {
        if (New == null) {
            New = new JButton("New game");
            New.setBackground(new Color(102, 255, 102));
            New.addActionListener(this);
        }
        return New;
    }
    /**
     * Button "Exit"
     */
    private JButton getEx() {
        if (Ex == null) {
            Ex = new JButton("Exit");
            Ex.setBackground(new Color(255, 102, 102));
            Ex.addActionListener(this);
        }
        return Ex;
    }
    /**
     * Main Panel witch contains pole(Buttons[])
     */
    private JPanel getJPanel1() {
        if (jPanel1 == null) {
            GridLayout gridLayout = new GridLayout(3,3,1,1);
            jPanel1 = new JPanel();
            jPanel1.setBackground(new Color(51, 51, 51));
            jPanel1.setLayout(gridLayout);

            pole = new JButton[9];
            for(int i=0;i<pole.length;i++){
                pole[i] = new JButton();
                setState(i, NULL);
                pole[i].addActionListener(this);
                pole[i].setForeground(new Color(200,200,255));
                jPanel1.add(pole[i]);
                pole[i].setOpaque(true);
                pole[i].setContentAreaFilled(false);
                pole[i].setBackground(new Color(102,255,102));
            }
        }
        return jPanel1;
    }
    /**
     * Function, witch set Buttons to X or O
     */
    private void setState(int index, int state){
        pole[index].setText(toStrTipe(state));
        pole[index].setEnabled(!(state==1||state==-1));
        ButtonsInGame--;
    }

////////////////////////////////////////////////////////////////////////////
//########################################################################//
////////////////////////////////////////////////////////////////////////////

    /**
     * This functions listen actions,
     */
    @Override
    public void actionPerformed(ActionEvent e) {
        if(e.getSource().equals(Ex))System.exit(0);
        if(e.getSource().equals(New))New_Game();
        for(int i=0 ; i<9 ; i++){
            if(e.getSource().equals(pole[i])){
                setState(i, O);
                set_mass();
                vin = lock_winner();
                if(vin!=0)StateGame.setText(vin_getter(vin));
                else{
                    setState(tactica1(), X);
                    set_mass();
                    int ret = lock_winner()	;
                    if(ret>0)StateGame.setText(vin_getter(ret));
                }

            }
        }

    }

    /**
     * This function play with player
     */
    private int tactica1(){
        if(ButtonsInGame==9)return 4;
        if(ButtonsInGame==8)
            while(true){
                switch(getRand(3)){
                    case 0:
                        if(m[0]==0)return 0;
                    case 1:
                        if(m[2]==0)return 2;
                    case 2:
                        if(m[6]==0)return 6;
                    case 3:
                        if(m[8]==0)return 8;
                        continue;
                }
            }
        if(ButtonsInGame<8){
            int res = empty_pole(X);
            if(res!=-1)return res;
            res = empty_pole(O);
            if(res!=-1)return res;
        }

        return rand_pole();
    }

//--------------------------------------------------------------//
    /**
     * This function work after "New game" pressed
     */
    protected void New_Game() {
        for(int i=0;i<9;i++){
            setState(i, NULL);
            pole[i].setContentAreaFilled(false);
        }
        ButtonsInGame = 9;
        StateGame.setText(vin_getter(3));
        int in = tactica1();
        if(in!=-1)setState(in, X);
    }
//////////////////////////////////////////////////////////////////////////
//----------------------------Serchers----------------------------------//
//////////////////////////////////////////////////////////////////////////
///---------------------------win-loocker------------------------------///
    /**
     * This function return win-constant or -1.
     */
    private int lock_winner()
    {
        if(ButtonsInGame==0)return 2;
        if(m[0]+m[1]+m[2]==3||m[0]+m[1]+m[2]==-3){
            line(0,1,2);
            return m[0];
        }
        if(m[0]+m[3]+m[6]==3||m[0]+m[3]+m[6]==-3){
            line(0,3,6);
            return m[0];
        }

        if(m[6]+m[7]+m[8]==3||m[6]+m[7]+m[8]==-3){
            line(6,7,8);
            return m[8];
        }
        if(m[2]+m[5]+m[8]==3||m[2]+m[5]+m[8]==-3){
            line(2,5,8);
            return m[8];
        }

        if(m[0]+m[4]+m[8]==3||m[0]+m[4]+m[8]==-3){
            line(0,4,8);
            return m[4];
        }
        if(m[2]+m[4]+m[6]==3||m[2]+m[4]+m[6]==-3){
            line(2,4,6);
            return m[4];
        }

        if(m[1]+m[4]+m[7]==3||m[1]+m[4]+m[7]==-3){
            line(1,4,7);
            return m[4];
        }
        if(m[3]+m[4]+m[5]==3||m[3]+m[4]+m[5]==-3){
            line(3,4,5);
            return m[4];
        }
        return NULL;
    }
    /**
     * This function returns text of state-label.
     */
    private String vin_getter(int stat)
    {
        if(stat==X)return "Win player x";
        if(stat==O)return "Win player o";
        if(stat==2)return "Erounded";
        if(stat==3)return "Unknown";
        return null;
    }
    /**
     * This function convert win-constant to win-symbol(X or O)
     */
    private String toStrTipe(int state){
        if(state==1)return "X";
        if(state==-1)return "O";
        return "";
    }
    /**
     * This function "draw win-line"
     */
    private void line(int a, int b, int c){
        pole[a].setContentAreaFilled(true);
        pole[b].setContentAreaFilled(true);
        pole[c].setContentAreaFilled(true);
    }
//////////////////////////////////////////////////////////////////////////
//-------------------------Utils----------------------------------------//
    /**
     * This function search win-pole to entered player
     */
    private int empty_pole(int player)
    {
        int n = (player==O)?-2:2; // equals = 2,-2

        System.out.print(toStrTipe(player)+": ");

        if(m[0]+m[1]+m[2]==n)
        {
            if(m[0]==0)
                return 0;
            if(m[1]==0)
                return 1;
            if(m[2]==0)
                return 2;
        }
        else if(m[3]+m[4]+m[5]==n)
        {
            if(m[3]==0)
                return 3;
            if(m[4]==0)
                return 4;
            if(m[5]==0)
                return 5;
        }
        else if(m[6]+m[7]+m[8]==n)
        {
            if(m[6]==0)
                return 6;
            if(m[7]==0)
                return 7;
            if(m[8]==0)
                return 8;
        }
        else if(m[0]+m[3]+m[6]==n)
        {

            if(m[0]==0)
                return 0;
            if(m[3]==0)
                return 3;
            if(m[6]==0)
                return 6;
        }
        else if(m[1]+m[4]+m[7]==n)
        {
            if(m[1]==0)
                return 1;
            if(m[4]==0)
                return 4;
            if(m[7]==0)
                return 7;
        }
        else if(m[2]+m[5]+m[8]==n)
        {
            if(m[2]==0)
                return 2;
            if(m[5]==0)
                return 5;
            if(m[8]==0)
                return 8;
        }
        else if(m[0]+m[4]+m[8]==n)
        {
            if(m[0]==0)
                return 0;
            if(m[4]==0)
                return 4;
            if(m[8]==0)
                return 8;
        }
        else if(m[6]+m[4]+m[2]==n)
        {
            if(m[6]==0)
                return 6;
            if(m[4]==0)
                return 4;
            if(m[2]==0)
                return 2;
        }
        return -1;
    }
//////////////////////////////////////////////////////////////////////////
//-------------------------Processing support---------------------------//
    /**
     * This function reset sub-pole for processing
     */
    private void set_mass()
    {
        for(int ii=0;ii<9;ii++)
        {
            if(pole[ii].getText().equals("X"))
                m[ii]=X;
            else if(pole[ii].getText().equals("O"))
                m[ii]=O;
            else
                m[ii]=NULL;
        }
        System.out.println("****************************************");
        System.out.println(" "+m[0]+" "+m[1]+" "+m[2]);
        System.out.println(" "+m[3]+" "+m[4]+" "+m[5]);
        System.out.println(" "+m[6]+" "+m[7]+" "+m[8]);
        System.out.println("****************************************");
    }
//////////////////////////////////////////////////////////////////////////
///-----------------------Random---------------------------------------///
    /**
     * return unsigned integer with limit "int lim"
     */
    private int getRand(int lim){
        if(lim<1)return -1;
        return Math.abs(rand.nextInt(lim));
    }
    /**
     *return random empty pole
     */
    private int rand_pole(){
        Vector <Integer> v = new Vector<Integer>();
        for(int i=0;i<9;i++)
            if(m[i]==0)v.add(i);
        return v.get(getRand(v.size()));
    }
}  //  @jve:decl-index=0:visual-constraint="4,10"