import React,{useEffect,useRef,useState} from 'react'
import io from 'socket.io-client';
import Peer from 'simple-peer';

const Video = (props) => {
    const ref = useRef();

    useEffect(() => {
        props.peer.on("stream", stream => {
            ref.current.srcObject = stream;
        })
    }, []);

    return (
        <video playsInline autoPlay ref={ref} />
    );
}

function Room(props) {
    const userStream = useRef(null);
    const [mystream,Setmystream]=useState(null);
    const [pause,Setpause]=useState(false);
    const [togglebtn,Settogglebtn]=useState('Stop Video');
    const [togglebtn2,Settogglebtn2]=useState('Pause Video');
    const SocketRef=useRef();
    const peersRef=useRef([]);
    const [peers,setPeers]=useState([]);
    useEffect(()=>{
        const roomId=props.match.params.roomID;
        SocketRef.current=io.connect("/");
        navigator.mediaDevices.getUserMedia({
            video:true,
            audio:true,
        }).then((stream)=>{
            userStream.current.srcObject=stream;
            Setmystream(stream);
            SocketRef.current.emit('join room',{roomId});
            SocketRef.current.on("all users in room",(allusers)=>{
                const peers=[]
                allusers.forEach(userid=>{
                    console.log(userid);
                    console.log(SocketRef.current.id);
                    const peer = createnewpeer(userid,SocketRef.current.id,stream);
                    console.log(peer);
                    peersRef.current.push({
                        peerID: userid,
                        peer,
                    })
                    peers.push(peer);
                })
                setPeers(peers);
            });
            SocketRef.current.on("user joined",({signal,callerId})=>{
            alert('user joined');
            const peer= addpeer(signal,callerId,stream);
            console.log(peer);
            peersRef.current.push({
                peerID: callerId,
                peer,
            })
            setPeers(users => [...users, peer]);
            });
    
            SocketRef.current.on("receiving returned signal",(payload)=>{
                console.log('the last stage');
                console.log(payload);
                const item = peersRef.current.find(p => p.peerID === payload.id);
                console.log(item);
                item.peer.signal(payload.signal);
            });
        }).catch(err=>console.log(err))
       
    },[])


    function addpeer(incomingSignal,callerId,stream){
        const peer = new Peer({
            initiator:false,
            trickle:false,
            stream
        });
        peer.on("signal",(signal)=>{
            SocketRef.current.emit("returning signal", { signal, callerId })
        })

        peer.signal(incomingSignal);
        return peer;

    }

    function createnewpeer(userToSignal,callerId,stream){
        const peer = new Peer({
            initiator:true,
            trickle:false,
            stream
        });
        peer.on("signal",(signal)=>{
            SocketRef.current.emit("sending signal", { userToSignal, callerId, signal });

        })
        return peer;

    }

    const pauseVideo=()=>{
        if(!pause){
            userStream.current.pause();
            Settogglebtn2('Play Video');
            Setpause(true);
        }
        else{
            userStream.current.srcObject=mystream;
            Settogglebtn2('Pause Video');
            Setpause(false);
        }
    }
    const stopVideo=()=>{
        if(!userStream.current.srcObject){
            userStream.current.srcObject=mystream;
            Settogglebtn('Start Video');
        }
        else{
            userStream.current.srcObject=null;
            Settogglebtn('Start Video');

        }
       
       
    }
    return (
        <>
        <video ref={userStream} controls autoPlay playsInline muted></video>
        {peers.map((peer, index) => {
                return (
                    <Video key={index} peer={peer} />
                );
        })}
        <button onClick={stopVideo}>{togglebtn}</button>
        <button onClick={pauseVideo}>{togglebtn2}</button>
        </>
    )
}

export default Room;
